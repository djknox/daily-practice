## Daily Practice
### Monday, September 14th, 2020
---


# [What's New In Laravel 8](https://laracasts.com/series/whats-new-in-laravel-8)  



### Preparing for Laravel 8
- update `laravel/installer` in `composer.json` to version `^4.0.0`  



### New `App\Models` directory
Model classes can now go inside `Models` directory  



### Migration squashing
`php artisan schema:dump`
- squashes all migration files into a database schema
- puts in `database\schema` directory



### Model factories
- model factories are now class-based
   * old: `factory('App\User')->make()`
   * new: `User::factory()->make()`
- add related children with `->has()`
   * example: `User::factory()->has(Post::factory()->count(5))->create()`
   * or with magic method: `User::factory()->hasPosts(5)->create()`
- add related parents with `->for()`
   * example: `Post::factory()->for(User::factory())->create()`
   * or with magic method: `Post::factory()->forUser()->create()`



### New maintenance mode options
`php artisan down`  
New options:
- `redirect[=REDIRECT]`
- `render[=RENDER]`
   * pre-renders a view or error message that will be displayed when in maintenance mode
   * blade view example: `php artisan down --render="maintenance"` will display the view at `views/maintenance.blade.php`
   * error example: `php artisan down --render="error::503"` will display the view for the 503 error code
- `retry[=RETRY]`
- `secret[=SECRET]`
   * creates a URI segment that will grant a cookie to a user to allow them access during maintenance mode
   * example: `php artisan down --secret=secret-entry` will grant a cookie to users that access `example.dev/secret-entry`
- `status[=STATUS]`



### Cleaner Closure-Based Event Listeners
Instead of making a Listener class for every Event, use `Event::listen()` and pass a closure
- example:
	```
	Event::listen(function (GiftCertificatePurchased $event) {
		// handle the event here
	})
	```


### Wormholes
`Wormhole` class provides ability to time-travel, useful for tests where timing matters  
- example - to advance 8 days in the future: `$this->travel()->days(8)`  



### Improved Rate Limiting
- Configuring throttle settings:
   * old: `Http\Middleware\Kernel.php`, `$middleWareGroups` has an `api` array that contains rate-limiting settings
   * new: `RouteServiceProvider` has a `configureRateLimiting()` method



### Jetstream
new application scaffolding
New options for `laravel new` command:
- `--dev`
- `--jet`
- `--stack[=STACK]`
- `--teams`
example: `laravel new jetstream --jet --stack=livewire --teams`
