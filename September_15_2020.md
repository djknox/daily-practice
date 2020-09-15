## Daily Practice
### Tuesday, September 15th, 2020
---


# [Laravel Explained](https://laracasts.com/series/laravel-explained){:target="_blank"}  


### Explain How a Facade Works Under the Hood

In Laravel, a facade provides a clean and quick way to access to an underlying object.

Facades are aliased in `'aliases'` array in `config/app.php`
- example: `'File' => Illuminate\Support\Facades\File::class`

When you reference a facade in the global namespace, it is available because it was defined as a class alias.
- Alias points to Facade class, Facade class points to the underlying object.
- Facade declares a key that will be stored in the Service Container.
- When the key is resolved, the key is checked against the Service Container and the underlying object instance is returned.

Service Providers can register keys and their respective values in the Service Container.



### Explain Real-Time Facades From the Inside-Out

Creating a facade:
1. create facade class that extends `Facade` base class
2. create `getFacadeAccessor()` static method - returns a key that will be referenced when the underlying class is being resolved out of the Service Container

Creating a real-time facade:
1. when importing the class, prepend `Facades`
   - example: `use App\Payment` --> `use Facades\App\Payment`


When using a real-time facade, Laravel dynamically creates a Facade file and places it in `storage/framework/cache` to be used.
