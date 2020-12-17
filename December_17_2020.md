## Daily Practice
### Thursday, December 17th, 2020
---


# [Browser Testing With Laravel Dusk](https://laracasts.com/series/browser-testing-with-laravel-dusk)


## [Learn the Basics](https://laracasts.com/series/browser-testing-with-laravel-dusk/episodes/1)

End-to-End/Browser tests

By default, Dusk uses Google Chrome and a standalone [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home) installation to run browser tests  

Screenshots will be taken for failed tests and placed within the `tests/Browser/screenshots` directory  

### Installation

`composer require --dev laravel/dusk`  
- add `laravel/dusk` Composer dependency  

`php artisan dusk:install`  
- create a `tests/Browser` directory with an example Dusk test  

`APP_URL=https://your-app-url.com`  
- set the `APP_URL` environment variable to the URL you use to access the application in the browser  


While using [Sail](https://laravel.com/docs/8.x/sail):  
- uncomment the Selenium service in `docker-compose.yml`  
- add `selenium` entry in `depends_on`  

`docker-compose.yml`  
```
selenium:
    image: 'selenium/standalone-chrome'
    volumes:
        - '/dev/shm:/dev/shm'
    networks:
        - sail
    depends_on:
        - mysql
        - redis
        - selenium
```
- run tests with `sail dusk`  


### Creating Tests

`php artisan dusk:make TheBrowserTestName`
- creates a test in the `tests/Browser` directory  

`tests/Browser/TheBrowserTestName.php`  
```
<?php

namespace Tests\Browser;

use Illuminate\Foundation\Testing\DatabaseMigrations;
use Laravel\Dusk\Browser;
use Tests\DuskTestCase;

use App\Models\User;

class TheBrowserTestName extends DuskTestCase
{
    use DatabaseMigrations;

    /** @test */
    public function a_user_can_register_correctly()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/register')
                ->type('name', 'User')
                ->type('email', 'user@example.com')
                ->type('password', 'password')
                ->type('password_confirmation', 'password')
                ->click('button[type="submit"]')
                ->assertSee('You have been registered.');
                ->click('#navbarDropdown')
                ->clickLink('Logout')
                ->assertSee('You have been logged-out');
        });
    }

    /** @test */
    public function a_user_can_login_correctly()
    {
        $user = User::factory()->create();

        $this->browse(function (Browser $browser) {
            $browser->visit('/')
                ->clickLink('/Login')
                ->type('name', $user->name)
                ->type('email', $user->email)
                ->type('password', $user->password)
                ->click('button[type="submit"]')
                ->assertSee('You have been registered.');
        });
    }
}
```


### Running Tests  

`php artisan dusk`
- run browser tests  

`php artisan dusk tests/Browser/TheTestIWantToRun.php`  
- run a specific test  

`php artisan dusk:fails`
- re-run tests that previously failed

`php artisan dusk --group=foo`  
`php artisan dusk --filter=foo`
- `dusk` accepts any argument that is normally accepted by the PHPUnit test runner  


### Using a separate database for Dusk tests  
 
In local environment, create a `.env.dusk.local` file in the root of the project
- forces Dusk to use its own environment file when running tests
- [Laravel Docs: Dusk - Environment Handling](https://laravel.com/docs/8.x/dusk#environment-handling)  
