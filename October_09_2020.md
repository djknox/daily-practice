## Daily Practice
### Friday, October 9th, 2020
---


# [Blade Component Cookbook](https://laracasts.com/series/blade-component-cookbook)


## [Social Media Card Meta Component](https://laracasts.com/series/blade-component-cookbook/episodes/10)

`meta` attributes for Twitter and Facebook social media cards  
 
[Twitter Cards documentation](https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/abouts-cards)
[Twitter card validator](https://cards-dev.twitter.com/validator)  

[The Open Graph protocol](https://ogp.me/)  

Example Twitter `meta` tags:
```
<meta name="twitter:card" content="summary" />
<meta name="twitter:site" content="@flickr" />
<meta name="twitter:title" content="Small Island Developing States Photo Submission" />
<meta name="twitter:description" content="View the album on Flickr." />
<meta name="twitter:image" content="https://farm6.staticflickr.com/5510/14338202952_93595258ff_z.jpg" />
```

Example Open Graph `meta` tags:
```
<meta property="og:card" content="summary" />
<meta property="og:site" content="@flickr" />
<meta property="og:title" content="Small Island Developing States Photo Submission" />
<meta property="og:description" content="View the album on Flickr." />
<meta property="og:image" content="https://farm6.staticflickr.com/5510/14338202952_93595258ff_z.jpg" />
```

In a Blade component:
```
<x-layout>
    <x-slot name="head">
        <meta name="twitter:card" content="summary" />
        <meta name="twitter:site" content="@laracasts" />
        <meta name="twitter:title" content="{{ $series->title }}" />
        <meta name="twitter:description" content="{{ $series->description }}" />
        <meta name="twitter:image" content="{{ $series->imagePath() }}" />

        <meta property="og:card" content="summary" />
        <meta property="og:site" content="@laracasts" />
        <meta property="og:title" content="{{ $series->title }}" />
        <meta property="og:description" content="{{ $series->description }}" />
        <meta property="og:image" content="{{ $series->imagePath() }}" />
        <meta property="og:url" content="{{ $series->urlPath() }}" />
    </x-slot>
</x-layout>
```

Twitter will fall back to some Open Graph tags if no equivalent Twitter-specific tags are provided (examples: `title`, `description`, `image`), so some tags can be removed:
```
<x-layout>
    <x-slot name="head">
        <meta name="twitter:card" content="summary" />
        <meta name="twitter:site" content="@laracasts" />

        <meta property="og:title" content="{{ $series->title }}" />
        <meta property="og:description" content="{{ $series->description }}" />
        <meta property="og:image" content="{{ $series->imagePath() }}" />
        <meta property="og:url" content="{{ $series->urlPath() }}" />
    </x-slot>
</x-layout>
```
- also removed `og:card` and `og:site` tags, as the Twitter-specific tags are provided

__Note:__ URLs need to be absolute (examples: `image` and `url` tags)  

Using a Blade component:
```
<x-layout>
    <x-social-media-meta-tags
        :title="$series->title"
        :description="$series->description"
        :image="$series->imagePath()"
        card="summary">
    </x-social-media-meta-tags>



    <x-slot name="head">
        <meta name="twitter:card" content="summary" />
        <meta name="twitter:site" content="@laracasts" />

        <meta property="og:title" content="{{ $series->title }}" />
        <meta property="og:description" content="{{ $series->description }}" />
        <meta property="og:image" content="{{ $series->imagePath() }}" />
        <meta property="og:url" content="{{ $series->urlPath() }}" />
    </x-slot>
</x-layout>
```

`social-media-meta-tags.blade.php`:
```
@props([
    'card' => 'summary_large_image',
    'title',
    'description',
    'image' => false
])

<meta name="twitter:card" content="{{ $card }}" />
<meta name="twitter:site" content="@laracasts" />

<meta property="og:title" content="{{ $title }}" />
<meta property="og:description" content="{{ $description }}" />

@if ($image)
    <meta property="og:image" content="{{ $image }}" />
@endif

<meta property="og:url" content="{{ url()->current() }}" />
```



## [Recaptcha Component](https://laracasts.com/series/blade-component-cookbook/episodes/11)

[Google - Recaptcha](https://www.google.com/recaptcha/about/)  

This example is using:
- reCAPTCHA v2
- Invisible reCAPTCHA badge
   * will only display if user seems "suspicious" (based on ML algorithm)

Receive a Site Key and Secret Key:
- Site Key: use in HTML of site
   * save as ENV variable, ex: `RECAPTCHA_KEY`
- Secret Key: use for communication between site and reCAPTCHA
   * save as ENV variable, ex: `RECAPTCHA_SECRET`

Reference the ENV variables from a config file, such as `config/services.php`:
```
'recaptcha' => [
    'key'    => env('RECAPTCHA_KEY'),
    'secret' => 'env('RECAPTCHA_SECRET'),'
]
```
- to use anywhere in the app, call `config('services.recaptcha.key)` and `config('services.recaptcha.secret)`

One approach to using Invisible reCAPTCHA - automatically bind the challenge to a `submit` button:
- pull in reCAPTCHA script
- reCAPTCHA intercepts the form submission
- creates challenge for user to complete
- after challenge confirmation, a callback is triggered in which the form can actually be submitted

`submit` button will need:
- `class="g-recaptcha"`
- `data-sitekey="the reCAPTCHA site key"`
- `data-callback="onSubmit"`
   * this is the callback that will be triggered after challenge confirmation

In main layout:
```
<script src="https://www.google.com/recaptcha/api.js" async defer></script>
<script>
    function onSubmit(token) {
        document.querySelector("form").submit();
    }
</script>
```

In form:
```
<button
    type="submit"
    class="g-recaptcha"
    data-sitekey="{{ config('services.recaptcha.key) }}"
    data-callback="onSubmit">
```

__Note:__ In the request, a `g-recaptcha-response` field will be added with a string value that can be used for verification on the server side

Verifying the user's response:
- grab `g-recaptcha-response`from request
- send API request to Google: POST to https://www.google.com/recaptcha/api/siteverify with:
   * `secret`: the Secret Key
   * `response`: the token in the`g-recaptcha-response` field
   * `remoteip`: IP of user (optional)

JSON response from Google API:
```
{
    "success": true|false,
    "challenge_ts": timestamp,
    "hostname": string,
    "error-codes": [...]
}
```

In Laravel, verifying the reCAPTCHA would be a good case for a Validation Rule  
`php artisan make:rule Recaptcha`
- creates Validation Rule stub at `app/Rules/Recaptcha.php`

`Recaptcha.php`:
```
public function passes($attribute, $value)
{
    // curl request to Google API
    $response = Http::asForm()->post('https://www.google.com/recaptcha/api/siteverify', [
        'secret'   => config('services.recaptcha.key),
        'response' => $value,
        'remoteip' => request()->ip(),
    ])->json();

    // check the response and return if successful
    return $response['success'];
}
```

Using the `Recaptcha` validation rule, in controller/route/etc.:
```
Route::post('/posts', function () {
    request()->validate([
        'title' => 'required',
        'body'  => 'required',
        'g-recaptcha-response' => ['required', new Recaptcha],
    ]);

    // after validation passes, create post, etc.
});
```
- `'g-recaptcha-response' => ['required', new Recaptcha]`
   * makes the `g-recaptcha-response` field required and passes to the `Recaptcha` validation rule to validate
   * in `Recaptcha@passes()`, the field is passed in as the `$value` parameter


Creating a component with Blade and Alpine - `recaptcha.blade.php`:
```
<div
    x-data="recaptcha()"
    x-init="init"
    @recaptcha.window="execute">
</div>

@push('scripts')
    <script src="https://www.google.com/recaptcha/api.js?render=explicit"></script>


    <script>
        window.recaptcha = () => {
            return {
                init() {
                    grecaptcha.ready(() => {
                        grecaptcha.render(this.$el, {
                            sitekey: '{{ config('services.recaptcha.key') }}',
                            size: 'invisible',
                            callback: this.onComplete.bind(this)
                        });
                    });
                },
                execute() {
                    grecaptcha.execute();
                },
                onComplete() {
                    this.$el.closest('form').submit();
                }
            };
        };
    </script>
@endpush
```

On the form:
```
<form
    method="POST"
    action="/posts"
    x-data
    @submit.prevent="$dispatch('recaptcha')">

    // form fields...

</form>
```
- instead of submitting the form, dispatch an event named `recaptcha`
- `recaptcha` event will bubble up to the `window`, where `recaptcha.blade.php` is listening for it and will call the `execute()` method
