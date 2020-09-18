## Daily Practice
### Friday, September 18th, 2020
---


# [Laravel Explained](https://laracasts.com/series/laravel-explained)  


### Explain How to Group Records By Relative Dates

Example: a feed of Laracasts episodes that are grouped by:
- Today
- Last Week
- Older


```
Video::latest()->get()->groupBy(function ($video) {
    return $video->id % 2 === 0;
});
```
- group videos by even and odd IDs


`Carbon` class has methods for determining relative dates
- `->isCurrentDay()`, `->isCurrentWeek()`, `->isNextYear()`, `->isFriday()`, etc.


```
Video::latest()->get()->groupBy(function ($video) {
    return $video->created_at->isCurrentMonth();
});
```
- group videos based on if they were created in the current month or not


```
Video::latest()->get()->groupBy(function ($video) {
    if ($video->created_at->isToday()) {
        return 'today';
    }

    if ($video->created_at->isCurrentWeek()) {
        return 'this week';
    }

    if ($video->created_at->isLastWeek()) {
        return 'last week';
    }

    return 'older';
});
```
- group videos based on the time they were created, giving names to each group
- example: the collection that contains today's videos will be named "today"


Ways to refactor:
- put a static method on the model class that contains the grouping logic
   * could then call something like `Video::recentByDate()`
- create a custom collection that has a method containing the grouping logic
   * will need to override the model class' `newCollection` method to tell it what custom collection class to use
   * could then call something like `Video::latest()->get()->recentByDate()`



### Explain Laravel Macros

`Macroable` trait
- allows you to extend the public interface of a class in real-time
- hook into a class and extend it with additional functionality in real-time
- use-cases:
   * working with code that you can't update directly but need to add functionality to
   * method you are adding doesn't make sense to be part of the overall code base (maybe a custom/one-off functionality)


Example:
```
Collection::macro('second', function () {
    return $this->items[1];
});

// ...later on in the file
return collect(['one', 'two', 'three'])->second();
```
- extend the `Collection` class with a `second()` method
- can call the `second()` method later on (in the same file) on a `Collection` instance



### Explain How to Limit Downloads Per User

`throttle` middleware is declared in `Kernel.php`'s `$routeMiddleware` array
- `\Illuminate\Routing\Middleware\ThrottleRequests::class`

Example:
```
Route::get('/downloads/1', 'DownloadsController@show')
    ->middleware(['throttle:3,1']);
```
- the user can only hit the `/downloads/1` endpoint 3 times in 1 minute before being given a `429 | Too Many Requests` response

Can provide a string to the `throttle` middleware's `maxAttempts` parameter and Laravel will look at the User model for an attribute to grab
- use-case: need to configure throttling based on the user's profile type (example: "Premium" users can make more downloads)
- example:
    ```
    // route definition
    Route::get('/downloads/1', 'DownloadsController@show')
        ->middleware(['throttle:download_limit,1']);

    ---

    // User model
    public function getDownloadLimitAttribute() {
        return 100;
    }
    ```
- the value for the `download_limit` attribute (defined by `getDownloadLimitAttribute`) will be placed into the `throttle` middleware call
