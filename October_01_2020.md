## Daily Practice
### Thursday, October 1st, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [Contribute to Laravel: From Start to PR](https://laracasts.com/series/how-do-i/episodes/20)

1. fork a copy of the repo
2. clone to local machine
3. make changes
4. commit with simple message
5. push to remote repo
6. submit PR
   * have a descriptive title
   * write a good description

Basic rule for Laravel PRs:
- if submitting a fix for a version of Laravel that has not been released yet, work on `master` branch
- if submitting a fix for a version of Laravel that _has_ been released, work on that release's branch

Most PRs get rejected - sometimes it is a good idea to create an issue first to determine if the PR is needed  



## [How Do I Create Laravel Macros](https://laracasts.com/series/how-do-i/episodes/21)

`Macroable` trait
- allows you to extend the public interface of a class in real-time
- hook into a class and extend it with additional functionality in real-time
- use-cases:
   * working with code that you can't update directly but need to add functionality to
   * method you are adding doesn't make sense to be part of the overall code base (maybe a custom/one-off functionality)


`Macroable` trait found at `/Illuminate/Support/Traits/Macroable.php`  
- `macro()` static function will register a custom macro
    ```
    /**
    * The registered string macros.
    *
    * @var array
    */
    protected static $macros = [];
    ```
    ```
    /**
    * Register a custom macro.
    *
    * @param  string    $name
    * @param  callable  $macro
    * @return void
    */
    public static function macro($name, callable $macro)
    {
        static::$macros[$name] = $macro;
    }
    ```
- `macro()` accepts two parameters:
   * `$name`: the name of the macro
   * `$macro`: this is usually a closure that will contain the macro's logic
- `macro()` will add the closure (`$macro`) to the `$macros` array with the `$name` as its key


Example of adding a macro to `Collection`:
```
Collection::macro('firstNth', function ($n) {
    return array_slice($this->items, 0, $n);
})
```
- Note: in the closure within a `macro()` function defintion, `$this` is properly bound to the object instance (in the example above, `$this` is bound to the `Collection` instance)
```
collect(['one', 'two', 'three'])->firstNth(2);  // returns a Collection containing ['one', 'two']
```

In order to make the macro chainable:
```
Collection::macro('firstNth', function ($n) {
    return new static(array_slice($this->items, 0, $n));
})
```
- in this context, `static` refers to `Collection`, so the macro will return a `Collection` instance


Example of adding a macro to `Filesystem`:
```
Filesystem::macro('make', function ($path) {
    return $this->put($path, '');
})
```
```
File::make(__DIR__.'/new.php');  // adds a file, "new.php", to the current directory
```


Good convention to place macro definitions in the `boot()` method of `AppServiceProvider`

`AppServiceProvider`:
- `register()`
   * when Laravel is booted up, all packages and service providers will be registered
- `boot()`
   * after everything has been registered, then the boot method will be called