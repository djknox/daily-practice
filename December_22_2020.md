## Daily Practice
### Tuesday, December 22nd, 2020
---


# [Browser Testing With Laravel Dusk](https://laracasts.com/series/browser-testing-with-laravel-dusk)


## [Pages and Components](https://laracasts.com/series/browser-testing-with-laravel-dusk/episodes/3)

### [Pages](https://laravel.com/docs/8.x/dusk#pages)  
- define expressive actions that can be performed on a given page via a single method
- define short-cuts to common selectors for your app or for a single page  

`php artisan dusk:page NameOfPage`
- generate a page object
- all page objects are placed in `tests/Browser/Pages` directory

Navigating to a page:
```
use Tests\Browser\Pages\Login;

$browser->visit(new Login);
```

__Pages have three default methods: `url()`, `assert()`, and `elements()`__  

`url()`
- should return the path of the URL that represents the page
```
/**
 * Get the URL for the page.
 *
 * @return string
 */
public function url()
{
    return '/login';
}
```

`assert()`
- (optional) make any assertions necessary to verify that the browser is actually on the given page
```
/**
 * Assert that the browser is on the page.
 *
 * @return void
 */
public function assert(Browser $browser)
{
    $browser->assertPathIs($this->url());
}
```


`elements()`
- define quick, easy-to-remember shortcuts for any CSS selector on your page
```
/**
 * Get the element shortcuts for the page.
 *
 * @return array
 */
public function elements()
{
    return [
        '@email' => 'input[name=email]',
    ];
}
```

Using in a test:  
```
$browser->type('@email', 'taylor@laravel.com');
```

__[Other page methods](https://laravel.com/docs/8.x/dusk#page-methods)__  

Can also define additional methods, usually representing common actions, that may be used throughout all tests  
Example - for a music management app, saving the logic for creating a playlist in a `createPlayList()` method:
```
<?php

namespace Tests\Browser\Pages;

use Laravel\Dusk\Browser;

class Dashboard extends Page
{
    // Other page methods...

    /**
     * Create a new playlist.
     *
     * @param  \Laravel\Dusk\Browser  $browser
     * @param  string  $name
     * @return void
     */
    public function createPlaylist(Browser $browser, $name)
    {
        $browser->type('name', $name)
                ->check('share')
                ->press('Create Playlist');
    }
}
```

Then the method can be used within any test that utilizes the page:
```
use Tests\Browser\Pages\Dashboard;

$browser->visit(new Dashboard)
        ->createPlaylist('My Playlist')
        ->assertSee('My Playlist');
```


### [Components](https://laravel.com/docs/8.x/dusk#components)  
- like pages, but intended for pieces of UI and functionality that are re-used throughout app
- components are not bound to specific URLs, but rather CSS selectors instead  

`php artisan dusk:component NameOfComponent`
- generate a component
- all components are placed in `tests/Browser/Components` directory

__Components have three default methods: `selector()`, `assert()`, and `elements()`__  

`selector()`
- should return the root selector for the component
```
/**
 * Get the root selector for the component.
 *
 * @return string
 */
public function selector()
{
    return '.date-picker';
}
```

`assert()`
- (optional) make any assertions necessary to verify that the browser contains the component
```
/**
 * Assert that the browser page contains the component.
 *
 * @param  Browser  $browser
 * @return void
 */
public function assert(Browser $browser)
{
    $browser->assertVisible($this->selector());
}
```


`elements()`
- define quick, easy-to-remember shortcuts for any CSS selector in the component
```
/**
 * Get the element shortcuts for the component.
 *
 * @return array
 */
public function elements()
{
    return [
        '@date-field' => 'input.datepicker-input',
        '@year-list' => 'div > div.datepicker-years',
        '@month-list' => 'div > div.datepicker-months',
        '@day-list' => 'div > div.datepicker-days',
    ];
}
```

__Other component methods__  

Can also define additional methods, usually representing common actions, that may be used throughout all tests  
Example - for a date picker component, saving the logic for choosing a date in a `selectDate()` method:
```
/**
 * Select the given date.
 *
 * @param  \Laravel\Dusk\Browser  $browser
 * @param  int  $year
 * @param  int  $month
 * @param  int  $day
 * @return void
 */
public function selectDate(Browser $browser, $year, $month, $day)
{
    $browser->click('@date-field')
            ->within('@year-list', function ($browser) use ($year) {
                $browser->click($year);
            })
            ->within('@month-list', function ($browser) use ($month) {
                $browser->click($month);
            })
            ->within('@day-list', function ($browser) use ($day) {
                $browser->click($day);
            });
}
```

Using a component in a test:
```
<?php

namespace Tests\Browser;

use Illuminate\Foundation\Testing\DatabaseMigrations;
use Laravel\Dusk\Browser;
use Tests\Browser\Components\DatePicker;
use Tests\DuskTestCase;

class ExampleTest extends DuskTestCase
{
    /**
     * A basic component test example.
     *
     * @return void
     */
    public function testBasicExample()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/')
                    ->within(new DatePicker, function ($browser) {
                        $browser->selectDate(2019, 1, 30);
                    })
                    ->assertSee('January');
        });
    }
}
```



## [Testing Your JavaScript](https://laracasts.com/series/browser-testing-with-laravel-dusk/episodes/4)
