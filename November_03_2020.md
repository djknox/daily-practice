## Daily Practice
### Tuesday, November 3rd, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [The Difference Between Debounce and Throttle](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/36)

lodash methods:  

[`_.throttle()`](https://lodash.com/docs/4.17.15#throttle): creates a throttled function that only invokes `func` at most once per every `wait` milliseconds  
- `_.throttle(func, [wait=0], [options={}])`


[`_.debounce()`](https://lodash.com/docs/4.17.15#debounce): creates a debounced function that delays invoking `func` until after `wait` milliseconds have elapsed since the last time the debounced function was invoked
- `_.debounce(func, [wait=0], [options={}])`


`throttle` and `debounce` are linked: `throttle` eventually defers to `debounce`

Examples:
```
updateFeaturedTestimonial: _.throttle(function (testimonial) {
    this.featuredTestimonial = testimonial;
}, 2000);
```
- `throttle` will trigger the function every 2000 milliseconds



```
updateFeaturedTestimonial: _.debounce(function (testimonial) {
    this.featuredTestimonial = testimonial;
}, 2000);
```
- `debounce` will wait until the last time the function was invoked, wait 2000 milliseconds, and then trigger the function
- with scroll events, click events, etc., `debounce` is usually the correct choice
