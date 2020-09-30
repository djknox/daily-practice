## Daily Practice
### Wednesday, September 30th, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Impersonate Users](https://laracasts.com/series/how-do-i/episodes/17)

Route + logic for setting up user-impersonation:
```
Route::get('impersonate/{user}', function (App\Models\User $user) {
    Auth::login($user);

    return redirect('/');
});
```

Tips:  
- add some sort of feedback that reminds you that you are impersonating another user (icon, color change, etc.)
- put a link for returning back to your original account and logging out the user that was being impersonating



## [How to Cleanly Refactor Loops](https://laracasts.com/series/how-do-i/episodes/18)

How to flatten an array:
```
function array_flatten($arr) {
    $flattened = [];

    foreach ($arr as $key => $val) {
        if (is_array($val)) {
            $flattened = array_merge($flattened, array_flatten($val));
        } else {
            $flattened[] = $val;
        }
    }

    return $flattened;
}
```
- initialize an empty array `$flattened`
- loop through the array `$arr`:
   * check if the value, `$val`, is an array
   * if `$val` is an array, recursively call `array_flatten`
   * merge the flattened `$val` array into `$flattened` with `array_merge()`
   * if `$val` is not an array, add it to `$flattened`


Could improve this by passing `$flattened` by reference as an argument to `array_flatten()`:
```
function array_flatten($arr, &$flattened = []) {
    foreach ($arr as $key => $val) {
        if (is_array($val)) {
            array_flatten($val, $flattened);
        } else {
            $flattened[] = $val;
        }
    }

    return $flattened;
}
```
- pass a variable by reference by adding `&` in front of the variable name

Could also remove the `else` statement and use `continue` to keep going:
```
function array_flatten($arr, &$flattened = []) {
    foreach ($arr as $key => $val) {
        if (is_array($val)) {
            array_flatten($val, $flattened);

            continue;
        }
        
        $flattened[] = $val;
    }

    return $flattened;
}
```



## [How Do I Make One Interface Fit Another](https://laracasts.com/series/how-do-i/episodes/19)

Scenario: need to adapt one interface, that you do not control, to fit another interface.  

__"Adapter Pattern"__  

Example:  
`Paperbook` and `Kindle` classes do the same thing, but have different APIs:
```
class Paperbook {
    public function open() {
        var_dump('Open the book');
    }

    public function turnPage() {
        var_dump('Turn the page');
    }
}
```
```
class Kindle {
    public function start() {
        var_dump('Start the ebook');
    }

    public function clickNext() {
        var_dump('Click to the next page');
    }
}
```
Use an "adapter" to take one interface and adapt it to another.  

Example:  
`EbookAdapter` will be responsible for adapting an eBook interface (could be a `Kindle` or something else) to one that we require (in this example, a `Paperbook`):
```
class EbookAdapter {
    public function __construct($ebook) {
        $this->ebook = $ebook;
    }

    public function open() {
        $this->ebook->start();
    }

    public function turnPage() {
        $this->ebook->clickNext();
    }
}
```
- bring in the method names that belong to the `Paperbook` interface and define them based on their corresponding methods in the eBook interface.

Using the `EbookAdapter` adapter:  
```
use App\Paperbook;
use App\Kindle;
use App\EbookAdapter;

$book = new Paperbook();
$book->open();
$book->turnPage();

$ebook = new EbookAdapter(new Kindle);
$ebook->open();
$ebook->turnPage();
```
- Can now use the same method names as `Paperbook` and `EbookAdapter` will wrap the `Kindle` object and delegate to the appropriate methods


Same example, but with `Paperbook` and `Kindle` implementing interfaces:  
```
interface Book {
    public function open();

    public function turnPage();
}
```
```
interface Ebook {
    public function start();

    public function clickNext();
}
```
```
class Paperbook implements Book {
    public function open() {
        var_dump('Open the book');
    }

    public function turnPage() {
        var_dump('Turn the page');
    }
}
```
```
class Kindle implements Ebook {
    public function start() {
        var_dump('Start the ebook');
    }

    public function clickNext() {
        var_dump('Click to the next page');
    }
}
```
```
class EbookAdapter implements Book {
    public function __construct(Ebook $ebook) {
        $this->ebook = $ebook;
    }

    public function open() {
        $this->ebook->start();
    }

    public function turnPage() {
        $this->ebook->clickNext();
    }
}
```
- adapter should implement the interface that it will be adapting to
- in this example, the `EbookAdapter` is responsible for adapting to a `Book`  

__Real-world use-cases:__  
- File systems:
   * Dropbox, local filesystem, S3, etc.
   * can use an adapter to make the Dropbox API conform to your own filesystem API
