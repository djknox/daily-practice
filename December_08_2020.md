## Daily Practice
### Tuesday, December 8th, 2020
---


# [Learn Laravel Horizon](https://laracasts.com/series/learn-laravel-horizon)


## [Installation, Usage, and Features](https://laracasts.com/series/learn-laravel-horizon/episodes/1)

- [github.com/laravel/horizon](https://github.com/laravel/horizon)  
- [Laravel docs - Horizon](https://laravel.com/docs/8.x/horizon)  
- [Redis](https://redis.io/)  

Horizon provides a dashboard and code-driven configuration for Redis queues
- monitor metrics such as job throughput, runtime, and job failures  
- configuration is stored in a single file that can stay in source control  

Redis must be installed on dev machine and production server (Redis is included with Homestead)  
`brew install redis`  

Start Redis CLI: `redis-cli`
- check if running with `ping` or `redis-cli ping` (will recieve `PONG` response if Redis is running)  

### Getting Redis to work with Laravel  

Two options:  
1. `phpredis` extension
- within `config/database.php`, there is a `redis` array and `phpredis` is declared as the default `REDIS_CLIENT`:
```
'redis' => [

    'client' => env('REDIS_CLIENT', 'phpredis'),

    ...
],
```
- must install `phpredis` with [PECL](https://pecl.php.net/)
   * `pecl list` - lists installed packages
   * installation not shown in this lesson
- `php -m | grep redis`
   * from the php modules that are running, search for `redis`

2. `predis` composer package
- `composer require predis/predis`
- update `redis` array in `config/database.php`:
```
'redis' => [

    'client' => env('REDIS_CLIENT', 'predis'),

    ...
],
```

Set `redis` as value for `QUEUE_CONNECTION` env variable:  
`.env`  
```
QUEUE_CONNECTION=redis
```

Simple test to see if Redis is working:  
```
...
use Illuminate\Support\Facades\Redis;

Route::get('/', function () {
    Redis::set('name', 'DJ');
    $name = Redis::get('name');

    dd($name);
});
```

Sending mail to test queue:  
```
Route::post('/', function () {
    Mail::to('dj@anddit.com`)->queue(new OrderShipped);

    return redirect('/');
});
```
- `->queue()` (instead of `->send()`) will put the email on the queue  

Telling Laravel to process the queue: `php artisan queue:work` 
- fires up queue worker to process the tasks


### Installing Horizon  

`composer require laravel/horizon`  
- installs Horizon composer package  

`php artisan horizon:install`  
- generates service provider, UI assets, and config file

`php artisan horizon`
- replaces `php artisan queue:work`
- fires up queue(s) and queue worker(s)

[Creating a job](https://laravel.com/docs/8.x/queues#creating-jobs):  
`php artisan make:job TheNameOfTheJob`  
- creates a new class in the `app/Jobs` directory

[Horizon Metrics](https://laravel.com/docs/8.x/horizon#metrics): 
- Horizon has a metrics dashboard that is populated by "snapshots"
   * to capture a snapshot: `php artisan horizon:snapshot`
- recommended to schedule snapshots to be captured every five minutes with the [scheduler](https://laravel.com/docs/8.x/scheduling):
    ```
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('horizon:snapshot')->everyFiveMinutes();
    }
    ```

[Job Batching](https://laravel.com/docs/8.x/queues#job-batching)  
- create a database migration to build a table that will contain job batch meta information:
    ```
    php artisan queue:batches-table
    php artisan migrate
    ```
- add `Illuminate\Bus\Batchable` trait to a job class to make it batchable
- dispatching a batch of jobs:
    ```
    $batch = Bus::batch([
        new ProcessPodcast(Podcast::find(1)),
        new ProcessPodcast(Podcast::find(2)),
        new ProcessPodcast(Podcast::find(3)),
        new ProcessPodcast(Podcast::find(4)),
        new ProcessPodcast(Podcast::find(5)),
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->catch(function (Batch $batch, Throwable $e) {
        // First batch job failure detected...
    })->finally(function (Batch $batch) {
        // The batch has finished executing...
    })->dispatch();
    ```
