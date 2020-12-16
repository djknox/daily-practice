## Daily Practice
### Wednesday, December 16th, 2020
---


# [What's New In Laravel 8](https://laracasts.com/series/whats-new-in-laravel-8)


## [Laravel Sail](https://laracasts.com/series/whats-new-in-laravel-8/episodes/12)

[Laravel Docs: Installation - Your First Laravel Project](https://laravel.com/docs/8.x/installation#your-first-laravel-project)  
- [Sail](https://laravel.com/docs/8.x/sail)  
- [Docker](https://www.docker.com/)  
- [Docker Desktop](https://www.docker.com/products/docker-desktop)  

Laravel Sail: light-weight command-line interface for interacting with Laravel's default Docker configuration  

Getting started with a new app (after installing Docker Desktop):  
```
// create a new `example-app` Laravel application
curl -s https://laravel.build/example-app | bash

// change directory to /example-app
cd example-app

// build Sail's application containers
./vendor/bin/sail up
```
- `example-app` test is available at `http://localhost`

`docker-compose.yml`  
- can configure docker in any way you like with this file  

Application is built based on `.env` configuration  
- `DB_DATABASE=my_project` = a database named `my_project` will be provisioned  
- can configure `.env` variables and `sail up` at the same time: `DB_DATABASE=my_db vendor/bin/sail up`  

After running `./vendor/bin/sail up`, it is possible that an error such as this will be thrown:  
`ERROR: for mysql  Cannot start service mysql: Ports are not available...`  
- this is most likely because valet, Homestead, etc. is still running and using the port(s)  
- to resolve, stop any conflicting services that may be running
   * `valet stop`
   * `vagrant down`
   * `brew services stop mysql@5.7` 

Running commands from within the container:  
`vendor/bin/sail {command}`  
- `php artisan migrate` --> `vendor/bin/sail artisan migrate`
- `php artisan tinker` --> `vendor/bin/sail artisan tinker`
- `npm install` --> `vendor/bin/sail npm install`

`vendor/bin/sail shell` (enter shell)  
- run commands like usual (`php artisan migrate`, `npm install`, etc.)  
- CTRL+D to exit shell  

Create an alias for `vendor/bin/sail`: `alias sail="vendor/bin/sail"`
- for my set up: `sudo nano ./bash_profile`
- may also configure in `./.bashrc` or `./zshrc`  


[Previewing Emails](https://laravel.com/docs/8.x/sail#previewing-emails)  
- `docker-compose.yml` contains a service entry for [MailHog](https://github.com/mailhog/MailHog)  
- [MailHog](https://github.com/mailhog/MailHog) intercepts email sent during local development and provides a web interface to preview in the browser
- default SMTP port is `1025`: `MAIL_PORT=1025`  
- access the MailHog interface at: localhost:8025 
```
Route::get('/', function () {
    Mail::raw('Hello world', function ($message) {
        $message
            ->to('foo@bar.com')
            ->from('admin@example.com')
            ->subject('Check This Out');
    });

    return view('welcome');
});
```
- after sending a `GET` request to `/`, can preview the sent email at localhost:8025 
