## Daily Practice
### Tuesday, September 29th, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Create A Snippets App: Part 1](https://laracasts.com/series/how-do-i/episodes/13)

`<pre>` tags display pre-formatted text  
`<code>` tags display code  
Example:
```
<pre>
    <code>
        function dd($data) {
            die(var_dump($data));
        }
    </code>
</pre>
```


## [How Do I Create A Snippets App: Part 2](https://laracasts.com/series/how-do-i/episodes/14)

Package for syntax highlighting: [`hightlight.js`](https://hightlightjs.org/)  
- looks for any markup contained within `<pre>` and `<code>` tags and adds syntax highlighting  



## [How Do I Convert Switch Statements to Polymorphism](https://laracasts.com/series/how-do-i/episodes/15)

Polymorphism - gives us a way to adopt a common interface that we can reference without needing to understand what is happening behind the scenes  
Just need to know:
- object conforms to the interface
- can do the expected behavior

__Factory class__ - a class which has the sole purpose of constructing an object  
- can be useful when the logic for how you create an object is complex
- can also keep it simple and just place logic inside a method

__Lookup table__  
Example:
```
$plans = [
    'free'     => 'GuestAccount',
    'monthly'  => 'Account',
    'lifetime' => 'LifetimeAccount',
];
```

Sometimes a few `if` statements or a `switch` statement is fine - if the code gets hard to read or not enjoyable to work with, then it is a good idea to refactor  



## [How Do I Reference Objects Like Arrays](https://laracasts.com/series/how-do-i/episodes/16)

```
$message = [
    'title' => '',
    'body' => '',
    'important' => false,
];
```

Can reference an object like an array:  
`$message->title`  
to  
`$message['title']`

by implementing the `ArrayAccess` interface:
```
class Message implements ArrayAccess {
    // ...
}
```
```
$m = new Message;
$m['title'] = 'New Message Title';
```
