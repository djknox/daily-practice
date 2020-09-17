## Daily Practice
### Thursday, September 17th, 2020
---


# [Laravel Explained](https://laracasts.com/series/laravel-explained)  


### Explain How the View Factory Works
`view()` helper function
- compiles Blade views to general PHP
- caches views for future requests
- converts views into appropriate response

`view()` is defined in `Illuminate/Foundation/helpers.php`:
```
function view($view = null, $data = [], $mergeData = [])
{
    $factory = app(ViewFactory::class);

    if (func_num_args() === 0) {
        return $factory;
    }

    return $factory->make($view, $data, $mergeData);
}
```

`view()` ultimately returns an instance of `View`
- `Illuminate/View/View.php`

`ViewFactory`
- `Illuminate/View/Factory.php`
- `make()` method:
   * normalizes the view name
   * creates view instance
   * calls view creator to set any data, etc.
   * returns view instance

`config/view.php` contains configuration for views
- storage paths (by default, Laravel's view storage path is `resources/views`)
- compiled view path (by default, Laravel's compiled view path is `storage/framework/views`)

`BladeCompiler` is defined in `Illuminate/View/Compilers/BladeCompiler.php`
- loads and compiles the blade file
- caches compiled view in `storage/framework/views` directory
