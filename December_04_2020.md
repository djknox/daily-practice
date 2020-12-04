## Daily Practice
### Friday, December 4th, 2020
---


# [Playing With PHP](https://laracasts.com/series/playing-with-php)


## [Hook Into PHPUnit's Pass/Fail Events](https://laracasts.com/series/playing-with-php/episodes/3)

Configuring a light from a Laravel route:  
```
Route::get('/', function () {
    Http::put('https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state', [
        'on'  => true,
        'hue' => 65535,
        'sat' => 254,
        'bri' => 20,
    ]');
});
```

Hooking into PHPUnit events:  
[PHPUnit - Extending the TestRunner](https://phpunit.readthedocs.io/en/9.3/extending-phpunit.html#extending-the-testrunner) - hooks that represent an event that can occur while tests are being executed  
Examples:  
- `AfterIncompleteTestHook`
- `AfterLastTestHook`
- `AfterRiskyTestHook`
- `AfterSkippedTestHook`
- `AfterSuccessfulTestHook`
- `AfterTestErrorHook`
- `AfterTestFailureHook`
- `AfterTestWarningHook`
- `AfterTestHook`
- `BeforeFirstTestHook`
- `BeforeTestHook`


Make an extension that hooks into the desired event:
create `Tests\MyExtension.php` class:  
```
<?php declare(strict_types=1);
namespace Tests;

use PHPUnit\Runner\AfterTestFailureHook;

final class MyExtension implements AfterTestFailureHook
{
    protected $hasError = false;

    public function executeAfterTestFailure(string $test, string $message, float $time): void
    {
        $this->hasError = true;

        Http::put('https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state', [
            'on'    => $on,
            'hue'   => 65535,
            'sat'   => 254,
            'bri'   => 20,
            'alert' => 'select'
        ]');
    }

    public function executeAfterLastTest(): void
    {
        public $hue;

        if ($this->hasError) {
            $hue = 65535;
        }
        else {
            $hue = 26870;
        }

        Http::put('https://192.168.1.4/api/km4AnsCcdbDtnyIcdr2Pt86uAWfWfF5bhWNPc1sA/lights/9/state', [
            'on'    => $on,
            'hue'   => $hue,
            'sat'   => 254,
            'bri'   => 20,
        ]');
    }
}
```
- `'alert' => 'select'` causes the light to blink  

Declare the extension within `<extensions></extensions` in `phpunit.xml`:  
```
<extensions>
    <extension class="Tests\MyExtension">
    </extension>
</extensions>
```
