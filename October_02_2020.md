## Daily Practice
### Friday, October 2nd, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Create a Star-Rating System: Part 1](https://laracasts.com/series/how-do-i/episodes/22)

Example: rating system for articles  
- Models: `Article`, `Rating`
    * an `Article` can have many `Rating`s
    * a `Rating` has one `User`
- API for rating an article: `$article->rate(5, $user);`
- Finding average rating for an article: `$article->ratings->avg('rating')`



## [How Do I Create a Star-Rating System: Part 2](https://laracasts.com/series/how-do-i/episodes/23)

Testing the API: `$this->post("/articles/{$article->id}/rate", ['rating' => 5]);`  

Route definition: `Route::post('/articles/{article}/rate', 'ArticleRatingController@store');`  

Form Validation in `ArticleRatingController@store`:
```
$request->validate([
    'rating' => ['required', 'in:1,2,3,4,5'],
]);
```


## [How Do I Create a Star-Rating System: Part 3](https://laracasts.com/series/how-do-i/episodes/24)

Rating form:
```
<form action="/articles/{{ $article->id }}/rate" method="POST">
    @csrf

    <div class="rating">
        <input type="radio" id="star5" name="rating" value="5" /><label for="star5"></label>
        <input type="radio" id="star4" name="rating" value="4" /><label for="star4"></label>
        <input type="radio" id="star3" name="rating" value="3" /><label for="star3"></label>
        <input type="radio" id="star2" name="rating" value="2" /><label for="star2"></label>
        <input type="radio" id="star1" name="rating" value="1" /><label for="star1"></label>
    </div>
</form>
```

Tip on Vue components:  
- any attribute you put on a Vue component that is not registered as a `prop` will be passed to the root element  
Example:  
`<star-rating action="/articles/{{ $article->id }}/rate"></star-rating>`
   * `action=...` attribute will be passed to the root `<form>` element within `StarRating.vue`
- to grab this attribute from within the component, can grab the root element and reference the attribute:  
   * `this.$el.action`



## [How Do I Make a Floating Nav Bar](https://laracasts.com/series/how-do-i/episodes/25)

Floating Nav Bar - a nav bar that re-introduces itself to the top of the page as you scroll further down the page

Making the nav bar always stick to the top of the page: `position: sticky;` + `top: 0;`   
Example:  
```
<nav style="position: sticky; top: 0;"></nav>
```

Add css-animations to make nav bar slide into the screen:
```
<style>
    @keyframes slide-nav-down {
        100% {
            transform: translateY(0);
        }
    }

    nav.is-floating {
        position: sticky;
        top: 0;
        transform: translateY(-100%);
        animation: slide-nav-down 3s;
        animation-fill-mode: forwards;
    }
</style>
```
- when `is-floating` class is added to the nav:
   * the nav will be `transform`ed out of view
   * then `slide-nav-down` will be animated over the course of `3s`
   * `animation-fill-mode: forwards;` makes the nav retain the styling from the end of the animation rather than immediately disappear after `slide-nav-down` finishes

[`IntersectionObserver`](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver)  
- provides a way to asynchronously observe changes in the intersection of a target element in the viewport (or a parent element)

Example:  
```
<div id="featured">
</div>

<script>
    let observer = new IntersectionObserver(entries => {
        console.log(entries);
    });

    observer.observe(document.querySelector('#featured'));
</script>
```
- observe the `#featured` div, and when it is intersected in the viewport, trigger the callback function

`IntersectionObserver` will create an `IntersectionObserverEntry` when there is an intersection  
- `IntersectionObserverEntry` has a `intersectionRatio` property that tracks how much intersection has happened
   * the larger the `intersectionRatio`, the more the element has been intersected in the viewport

May want to wait to trigger the callback until more of the observed element has been intersected:
- can provide a threshold as the second argument to the `IntersectionObserver` constructor:
    ```
        let observer = new IntersectionObserver(entries => {
            if (entries[0].intersectionRatio >= 0.5) {
                document.querySelector('nav').classList.add('is-floating');
            }
            else {
                document.querySelector('nav').classList.remove('is-floating');
            }
        }, {
            threshold: 0.5
        });
    ```
   * the `IntersectionObserverEntry` will not be created until the `intersectionRatio` reaches 0.5

Can also use this to:
- lazy-load images: only download images when they have entered the viewport

Can cache the nav bar query to prevent it from running multiple times:
```
<script>
    (function (nav) {
        let observer = new IntersectionObserver(entries => {
            if (entries[0].intersectionRatio >= 0.5) {
                nav.classList.add('is-floating');
            }
            else {
                nav.classList.remove('is-floating');
            }
        }, {
            threshold: 0.5
        });

        observer.observe(document.querySelector('#featured'));
    })(document.querySelector('nav'));
</script>
```
- this will query for the navbar once by caching it in a `nav` variable outside of the callback function