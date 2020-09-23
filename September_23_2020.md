## Daily Practice
### Wednesday, September 23rd, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Unit Test Eloquent Models?](https://laracasts.com/series/how-do-i/episodes/5)

Don't try to unit test Eloquent - use integration tests and actually hit a database.



## [Help Me Understand CSRF](https://laracasts.com/series/how-do-i/episodes/6)

CSRF = Cross-Site Request Forgery

In `Kernel.php`'s `$middleWareGroups` array, the `'web'` group contains the `VerifyCsrfToken` middleware class  

In `RouteServiceProvider.php`, `mapWebRoutes()` applies the `'web'` group to all routes in `routes/web.php`  

`\App\Http\Middleware\VerifyCsrfToken::class`:
- contains an `$except` array property that you can list any URIs that should be excluded from CSRF verification
- extends `Illuminate\Foundation\Http\Middleware\VerifyCsrfToken`
   * almost all CSRF verification logic is done in this parent class:
      - checks the session token against the token submitted in the form to ensure they match
      - adds the CSRF token to the response cookies

Laravel's `csrf_field()` helper (now `@csrf` blade directive) adds a hidden field with the CSRF token inside a form

CSRF middleware is automatically disabled when running tests
