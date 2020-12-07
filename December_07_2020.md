## Daily Practice
### Monday, December 7th, 2020
---


# [PHP 8 Crash Course](https://laracasts.com/series/php8-crash-course)


## [How to Install PHP 8](https://laracasts.com/series/php8-crash-course/episodes/1)

Check current PHP version: `php -v`  

Upgrade to PHP 8 using Homebrew:
- `brew update`
- `brew install php`
OR
- `brew upgrade php`



## [The Nullsafe Operator](https://laracasts.com/series/php8-crash-course/episodes/2)

The [Nullsafe operator](https://wiki.php.net/rfc/nullsafe_operator): `?->`
- only call a method or fetch a property on the result of an expression if it is not `null`

Can rewrite:  
```
$country =  null;
 
if ($session !== null) {
    $user = $session->user;
 
    if ($user !== null) {
        $address = $user->getAddress();
 
        if ($address !== null) {
            $country = $address->country;
        }
    }
}
 
// do something with $country
```

to:  
```
$country = $session?->user?->getAddress()?->country;
 
// do something with $country
```

The nullsafe operator returns `null` (instead of throwing errors) if an expression results to `null`  



## [Match Expressions](https://laracasts.com/series/php8-crash-course/episodes/3)

[Match expression](https://wiki.php.net/rfc/match_expression_v2)  
- like `switch`, but with safer semantics and ability to return values  

`switch`:  
```
switch ($this->lexer->lookahead['type']) {
    case Lexer::T_SELECT:
        $statement = $this->SelectStatement();
        break;
 
    case Lexer::T_UPDATE:
        $statement = $this->UpdateStatement();
        break;
 
    case Lexer::T_DELETE:
        $statement = $this->DeleteStatement();
        break;
 
    default:
        $this->syntaxError('SELECT, UPDATE or DELETE');
        break;
}
```

rewritten using `match`:  
```
$statement = match ($this->lexer->lookahead['type']) {
    Lexer::T_SELECT => $this->SelectStatement(),
    Lexer::T_UPDATE => $this->UpdateStatement(),
    Lexer::T_DELETE => $this->DeleteStatement(),
    default => $this->syntaxError('SELECT, UPDATE or DELETE'),
};
```



## [Constructor Property Promotion](https://laracasts.com/series/php8-crash-course/episodes/4)

[Constructor property promotion](https://wiki.php.net/rfc/constructor_promotion) - short hand syntax that simplifies the required boilerplate in class definitions  

When a method parameter is prefixed with one of the visibility keywords `public`, `protected` or `private`, it is considered to be “promoted”  

Before:  
```
class Point {
    public float $x;
    public float $y;
    public float $z;
 
    public function __construct(
        float $x = 0.0,
        float $y = 0.0,
        float $z = 0.0,
    ) {
        $this->x = $x;
        $this->y = $y;
        $this->z = $z;
    }
}
```
- repeats properties in:
   1. property declaration
   2. constructor parameters
   3. 2x in the property assignment
   4. property type repeated 2x

Using shorthand:  
```
class Point {
    public function __construct(
        public float $x = 0.0,
        public float $y = 0.0,
        public float $z = 0.0,
    ) {}
}
```


## [$object:class](https://laracasts.com/series/php8-crash-course/episodes/5)

[Allow ::class on objects](https://wiki.php.net/rfc/class_name_literal_on_object) - can use `::class` directly on an object - same as `get_class()`  

Example:
```
class Conversation {};

$obj = new Conversation;

// returns Conversation
var_dump(get_class($obj));

// returns Conversation
var_dump($obj::class);
```
- good idea to replace any old instances of `get_class($foo)` with `$foo::class`



## [Named Parameters](https://laracasts.com/series/php8-crash-course/episodes/6)

[Named parameters](https://wiki.php.net/rfc/named_params) - allow passing arguments to a function based on parameter name rather than position  
- makes the meaning of the argument self-documenting  
- makes the arguments order-independent
- allows skipping default values arbitrarily

Using positional arguments: `array_fill(0, 100, 50);`
Using named arguments: `array_fill(start_index: 0, num: 100, value: 50);`  
Named arguments in different order: `array_fill(value: 50, num: 100, start_index: 0);`  

Keep in mind:  
- using named parameters couples your code to the parameter names - any changes in parameter names would be a breaking change  
- could be a code smell that the class/function is doing too much if there is a need to name its parameters  

Useful if:  
- need to pass a value for a parameter that is ordered after other parameters, without passing a value to the preceding parameters (example: can pass a value to the last argument without passing values to the others)  
- similarly, only need to set some parameter values and leave others  



## [New String Helpers](https://laracasts.com/series/php8-crash-course/episodes/7)

### `str_contains()`  

Determine if a URL has a query string -  

Using `parse_url()`:
```
$url = 'https://example.com?foo=bar';

// returns the query string
var_dump(parse_url($url)['query'])
```

Using `strpos()`:  
```
$url = 'https://example.com?foo=bar';

// returns true
var_dump(strpos($url, '?') !== false);
```

Using `str_contains()`:  
```
$url = 'https://example.com?foo=bar';

// returns true
var_dump(str_contains($url, '?');
```


### `str_starts_with()`  

Determine if a string, `$id`, begins with 'inv_' -  
Old way:  
```
$id = 'inv_kajsdfi';

$result = stripos($id, 'inv_) === 0;

// returns true
var_dump($result);
```

Using `str_starts_with()`:  
```
$id = 'inv_kajsdfi';

$result = str_starts_with($id, 'inv_);

// returns true
var_dump($result);
```


### `str_ends_with()`  

Determine if a string, `$id`, ends with '_ch' -  
Old way:  
```
$id = 'kajsdfi_ch';

$result = stripos(strrev($id), strrev('_ch')) === 0;

// returns true
var_dump($result);
```
Using reg expressions:  
```
$id = 'kajsdfi_ch';

$result = !! preg_match('/_ch%/', $id));

// returns true
var_dump($result);
```

Using `str_ends_with()`:  
```
$id = 'ch_kajsdfi';

$result = str_ends_with($id, 'ch_);

// returns true
var_dump($result);
```



## [Weak Maps](https://laracasts.com/series/php8-crash-course/episodes/8)

`WeakMap`s are a new type of key-value stores:
- keys are objects
- allows for garbage collection

Previously, in order to have an object be the key in a key-value store, would use `SplObjectStorage()`:  
```
$obj = new stdClass();

$store = new SplObjectStorage();

$store[$obj] = 'foobar';
```
- with `SplObjectStorage()`, no garbage collection takes place
   * can `unset()` a key from storage, but a reference will still exist  
   * `WeakMap`s solve this issue of garbage collection  

Example of when to use `WeakMap`s:  
- want to track how many times an event has been dispatched
- associating an object, the event, to a value for the count
- use a `WeakMap` to track the event as the key, and the number of times the event has been dispatched as its value  

PHP Tip: [Null Coalescing Assignment Operator (`??=`)](https://wiki.php.net/rfc/null_coalesce_equal_operator)  
- if the left parameter is null, assigns the value of the right paramater to the left one
- if the value is not null, nothing is done
Example:  
```
$this->request->data['comments']['user_id'] = $this->request->data['comments']['user_id'] ?? 'value';
```
is the same as:
```
$this->request->data['comments']['user_id'] ??= 'value';
```



## [Union and Psuedo Types](https://laracasts.com/series/php8-crash-course/episodes/9)

"I expect this type OR that type" - can specify that a method parameter may accept multiple types  

Example:  
```
class User
{
    public function makeFriendsWith(User $user)
    {
        var_dump('Yay friends');
    }
}
```

Can make the `User $user` parameter optional/nullable by prepending it with `?`:  
```
class User
{
    public function makeFriendsWith(?User $user)
    {
        var_dump('Yay friends');
    }
}
```

In PHP 8, this can be written as:
```
class User
{
    public function makeFriendsWith(User|null $user)
    {
        var_dump('Yay friends');
    }
}
```
- `makeFriendsWith()` can accept either a `User` or `null` instance

This can be written in older versions of PHP by using docblocks:  
```
class User
{
    /**
     * @param User|null $user
     */
    public function makeFriendsWith($user)
    {
        var_dump('Yay friends');
    }
}
```

Using `mixed` keyword as a catch-all:  
```
class User
{
    public function makeFriendsWith(mixed $user)
    {
        var_dump('Yay friends');
    }
}
```
- other psuedo types include `iterable`, `callable`, etc.  
