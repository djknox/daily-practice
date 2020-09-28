## Daily Practice
### Monday, September 28th, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Sort Tags By Letter](https://laracasts.com/series/how-do-i/episodes/10)

Tip:  
When linking stylesheets, the `type=text/css` bit is unnecessary.  
- `<link rel="stylesheet" type="text/css" href="/app.css">`
- `<link rel="stylesheet" href="/app.css">`

Possible to pass a closure to the `groupBy()` method to group collections based on any criteria you create.  
Example:  
```
$tags = Tag::all()->groupBy(function ($tag) {
    return substr($tag->name, 0, 1);
});
```
- this will group the tags into subcollections where the key is the first letter of the subcollection's words  



## [How Do I Dry Up My Forms](https://laracasts.com/series/how-do-i/episodes/11)

Scenario: have forms for `/edit` and `/create` -> how to DRY these up?  
- Extract form markup to a Blade partial
- keep `<form>` wrappers in their respective views
   * this way, can easily define the `method` and `action` attributes for each form
- pass in values to blade partial for submit button text:
    ```
    @include ('posts.form', [
        'submitButtonText' => 'Update Post'
    ])
    ```
- pass in values to blade partial for form values
    ```
    @include ('posts.form', [
        'post' => $post
    ])
    ```
   * pass in field values to forms:
        ```
        <input value="{{ old('title') ?? $post->title }}">
        ```
        - The `old()` method checks to see if there is any older input - this would _not_ be what is stored in the database, but rather the input contained in the request
   * for the `/create` form, pass a null object to avoid `Undefined variable` exception:
        ```
        @include ('posts.form', [
            'post' => new App\Post
        ])
        ```



## [How Do I Automatically Resize Textareas to Fit Their Contents](https://laracasts.com/series/how-do-i/episodes/12)

Package: `autosize`
- `npm install autosize`
- `autosize()` function

In `app.js`:
```
import autosize from 'autosize';

autosize(document.querySelectorAll('textarea'));
```

`autosize()` will adjust the height of textareas based on their contents
