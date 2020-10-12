## Daily Practice
### Monday, October 12th, 2020
---


# [Learn Socialite](https://laracasts.com/series/learn-socialite)


## [Log In With GitHub](https://laracasts.com/series/learn-socialite/episodes/1)

Authenticate with OAuth providers - current supports authentication with:
- Facebook
- Twitter
- LinkedIn
- Google
- GitHub
- GitLab
- Bitbucket

Adapters for other providers (community driven):  
[https://socialiteproviders.com/](https://socialiteproviders.com/)

Add Socialite to project dependencies with Composer:  
`composer require laravel/socialite`

In `config/services.php`, add credentials for the OAuth service(s) that will be utilized.  
Example:  
```
'github' => [
    'client_id' => env('GITHUB_CLIENT_ID'),
    'client_secret' => env('GITHUB_CLIENT_SECRET'),
    'redirect' => 'http://your-callback-url',
],
```
- `redirect` can be a relative path, which is useful for different environments (dev/test/staging/etc.)

To set up on GitHub side:  
- go to GitHub > Settings > Developer settings > OAuth Apps > "New OAuth App" button
- "Authorization callback URL": set to Laravel route for callback
   * Example: `my-laravel-app.test/login/github/callback`
   * save this URL as an environment variable
- will receive Client ID + Client Secret after registering the application
   * save these as environment variables

Environment variables to add:
- `GITHUB_CLIENT_ID`
- `GITHUB_CLIENT_SECRET`
- `GITHUB_REDIRECT`

Create a controller for logging in to GitHub.  
From [Laravel docs](https://laravel.com/docs/8.x/socialite#routing):
```
<?php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Laravel\Socialite\Facades\Socialite;

class LoginController extends Controller
{
    /**
     * Redirect the user to the GitHub authentication page.
     *
     * @return \Illuminate\Http\Response
     */
    public function redirectToProvider()
    {
        return Socialite::driver('github')->redirect();
    }

    /**
     * Obtain the user information from GitHub.
     *
     * @return \Illuminate\Http\Response
     */
    public function handleProviderCallback()
    {
        $user = Socialite::driver('github')->user();

        // $user->token;
    }
}
```

Corresponding routes for controller methods:
- `{provider}` argument will allow for using multiple providers
```
use App\Http\Controllers\Auth\LoginController;

Route::get('login/{provider}', [LoginController::class, 'redirectToProvider']);
Route::get('login/{provider}/callback', [LoginController::class, 'handleProviderCallback']);
```

A few changes to the `users` table migration:  
- `password` can be `nullable`, as OAuth authenticated users will not need one
   * `$table->string('password')->nullable();`
- will want to save the provider's ID (this comes in the response after OAuth authentication)
   * `$table->string('provider_id')->nullable();`
- will want to save the provider's name
   * `$table->string('provider')->nullable();`

In `LoginController@handleProviderCallback()`, handle the next steps after OAuth authentication.  
Such as:
- creating a new user in the database (first check if the user already exists)
- logging the user in
- redirecting

Example:  
```
/**
 * Obtain the user information from GitHub.
 *
 * @return \Illuminate\Http\Response
 */
public function handleProviderCallback($provider)
{
    $socialiteUser = Socialite::driver($provider)->user();

    $user = User::firstOrCreate(
        [
            'provider' => $provider,
            'provider_id' => $socialiteUser->getId(),
        ],
        [
            'email'       => $socialiteUser->getEmail(),
            'name'        => $socialiteUser->getName(),
        ]
    );

    auth()->login($user, true);

    return redirect('dashboard');
}
```

Note on `firstOrCreate()`:
- the first argument is an array of the fields to check for a matching record
- the second argument is an array of the fields to instantiate a new record with (they will also be combined with the fields in the first argument)



## [Use Third-Party Providers](https://laracasts.com/series/learn-socialite/episodes/2)

Adapters for other providers (community driven):  
[https://socialiteproviders.com/](https://socialiteproviders.com/)

This lesson will show authentication with Twitch.  

Add Twitch package to project dependencies with Composer:  
`composer require socialiteproviders/twitch`

In `config/services.php`, add Twitch credentials:
```
'twitch' => [
    'client_id' => env('TWITCH_CLIENT_ID'),
    'client_secret' => env('TWITCH_CLIENT_SECRET'),
    'redirect' => env('TWITCH_REDIRECT_URI'),
],
```
- also need to add the environment variables to `env` file

Configuring from Twitch side:
- [https://dev.twitch.tv/](https://dev.twitch.tv/)
- "Your Console" > Applications > "+ Register Your Application"
- add a redirect URL
   * Example: `my-laravel-app.test/login/twitch/callback`
   * save this URL as `TWITCH_REDIRECT_URI` environment variable
- will receive Client ID + Client Secret after registering the application
   * save these as `TWITCH_CLIENT_ID` + `TWITCH_CLIENT_SECRET` environment variables

Add event listener for `SocialiteWasCalled` events in the `$listen` array in `app/Providers/EventServiceProvider`:
```
protected $listen = [
    \SocialiteProviders\Manager\SocialiteWasCalled::class => [
        'SocialiteProviders\\Twitch\\TwitchExtendSocialite@handle',
    ],
];
```

Add `\SocialiteProviders\Manager\ServiceProvider::class` to the `providers[]` array in `config\app.php`:
```
'providers' => [
    \SocialiteProviders\Manager\ServiceProvider::class,
];
```
