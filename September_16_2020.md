## Daily Practice
### Wednesday, September 16th, 2020
---


# [Laravel Explained](https://laracasts.com/series/laravel-explained)  


### Explain PHP Generators
A generator allows circumventing memory limit concerns by iterating over data without first building up a large array in memory.  

Main use-case is when dealing with very large datasets that would normally exceed data limits.  

`yield` keyword is used  

Examples:
```
function customRange($begin, $end) {
    for ($i = $begin; $i <= $end; $i++) {
        yield $i;
    }
}
```
- instead of returning an array, `customRange()` returns a `Generator` object that can be iterated over

`Generator` objects can be iterated over, for example:
```
foreach (customRange(1, 10000) as $i) {
    dump($i);
}
```
`->current()` and `->next()`can be used on a `Generator` to get the current and next values  

`iterator_to_array()` will cast an iterator, such as a `Generator`, to an array  



### Explain How a Route Returns a Response

Any object that implements the `Responsable` interface (with one method, `toResponse()`) can be converted to a response  

`index.php` is the entry-point for a Laravel application
- creates the application
- resolves the kernel
- handles incoming request through the kernel and sends response back to browser

`Kernel.php` is the HTTP core for a Laravel application
- kernel receives the request from `index.php`
- sends request through middleware and then to `Router.php` and ultimately returns a response to `index.php`

`Router.php`
- matches request to a route (if possible)
- if route is found, request is sent through middleware before being passed to route
- prepares an appropriate response (based on what is returned from the route) with `toResponse()`
   * example: if the route returns an array, a `JsonResponse` will be returned

Overview:
1. request comes in
2. application is created and kernel is resolved
3. kernel handles the request and prepares the response
4. response is sent to user
