## Daily Practice
### Wednesday, December 9th, 2020
---


# [Learn Laravel Horizon](https://laracasts.com/series/learn-laravel-horizon)


## [Horizon Notifications](https://laracasts.com/series/learn-laravel-horizon/episodes/2)

### Become notified if MAX WAIT TIME reaches a certain time  
`HorizonServiceProvider.php`  
```
public function boot()
{
    parent::boot();

    Horizon::routeMailNotificationsTo('example@example.com');
}
```
- configures an email address to receive mail notifications from Horizon

`config/horizon.php`  
```
'waits' => [
    'redis:default' => 60,
],
```
- configures the default maximum wait time (in seconds)  
- when the max wait time exceeds 60 seconds, an email will be sent (because this was configured in `HorizonServiceProvider`)  


### Become notified of failed jobs:  
Create a new Mailable:  
- `php artisan make:mail JobFailedMailable --markdown=emails.job-failed` 

`App\Mail\JobFailedMailable.php`  
```
use Illuminate\Queue\Events\JobFailed;

class JobFailedMailable extends Mailable
{
    public $event;

    public function __construct(JobFailed $event)
    {
        $this->event = $event;
    }

    public fnction build()
    {
        return $this->subject('A job failed')->markdown('emails.job-failed');
    }
}
```

Build out the email template:  
`views/emails/job-failed-blade.php`  
```
@component('mail:message')
# A job failed

Job Class: {{ $event->job->resolveName() }}

Job Body: {{ $event->job->getRawBody() }}

Exception: {{ $event->exception->getTraceAsString() }}

@endcomponent
```


Hook into `Queue` facade's `failing()` method and register an event listener to send the mail:  
`AppServiceProvider.php`  
```
use Illuminate\Support\Facades\Mail;
use App\Mail\JobFailedMailable;

public function boot()
{
    Queue::failing(function (JobFailed $event) {
        Mail::to('example@example.com')->send(new JobFailedMailable($event));
    })
}
```
 


## [Using Horizon with Laravel Forge](https://laracasts.com/series/learn-laravel-horizon/episodes/3)

`HorizonServiceProvider`'s `gate()` method determines who can access Horizon in non-local environments  

`Providers/HorizonServiceProvider.php`  
```
protected function gate()
{
    Gate::define('viewHorizon', function ($user) {
        return in_array($user->email, [
            'example@example.com'
        ]);
    });
}
```

[Deploying Horizon](https://laravel.com/docs/8.x/horizon#deploying-horizon)  
- when deploying Horizon to production, configure a process monitor to monitor the `php artisan horizon` command and restart it if it exits unexpectedly
- Laravel Forge will automatically install and configure a process monitor named Supervisor  

In Laravel Forge's navigation, choose "New Daemon":  
- add `php artisan horizon` as the "Command"
- add other relevant information such as user (`forge`), app directory, etc.
- click "START DAEMON" button

Be sure to update environment variables in production  
```
QUEUE_CONNECTION=redis
```

Terminate Horizon workers every time code is deployed:  
- Add to deploy script (in my case, this will be done in Envoyer): `php artisan horizon:terminate`  
- Horizon workers will be terminated initially, and daemon will pick up from there and restart everything
