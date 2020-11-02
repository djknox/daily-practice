## Daily Practice
### Monday, November 2nd, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Create a Testimonials Component With Me](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/35)

Create a grid of testimonial icons/links - when one is hovered over, have it display in a larger window next to the grid.  

`testimonials.blade.php`
```
<section>
    <div class="container">

        <testimonials></testimonials>
    
    </div>
</section>
```

`Testimonials.vue`
```
<template>
    <a 
        v-for="(testimonial, index) in testimonials"
        :href="testimonial.link"
        :id="`testimonial-${++index}`"
        @mouseover="updateFeaturedTestimonial(testimonial)"
        @mouseout="clearTimer">
    >
        <img
            :src="`/images/reviews/${testimonial.avatar}`"
            :alt="testimonial.name"
            :class="featuredTestimonial.id === testimonial.id ? 'tw-border-blue-darker' : 'tw-border-transparent'"
        >
    </a>
</template>

<script>
    import _ from 'lodash';

    export default {
        data () {
            return {
                testimonials: [],
                featuredTestimonial = {},
            };
        },

        created () {
            $.getJSON('/api/testimonials', testimonials => {
                this.testimonials = testimonials;
                this.featuredTestimonial = testimonials[0];
            });
        },

        methods: {
            updateFeaturedTestimonial (testimonial) {
                setTimeout(() => {
                    this.featuredTestimonial = testimonial;
                }, 200);
            },

            clearTimer () {
                clearTimeout(this.timer);
            }
        },
    };
</script>
```
- when the component is `created ()`, load the testimonials from the API
   * NOTE: this example is using `jQuery` rather than `axios` for the API call

[`lodash`'s `_.debounce()` function](https://lodash.com/docs/#debounce):
- delays the invoking of a function until after a certain number of milliseconds

`updateFeaturedTestimonial` using `debounce`:
```
updateFeaturedTestimonial: _.debounce(function (testimonial) {
    this.featuredTestimonial = testimonial;
}, 200);
```
- testimonial link is hovered over, `updateFeaturedTestimonial()` is called
- wait 200 milliseconds before setting `featuredTestimonial` to the one hovered-over

[`setTimeout()` function](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout):
- sets a timer which executes a function/piece of code once the timer expires

`updateFeaturedTestimonial` using `setTimeout`:
```
updateFeaturedTestimonial (testimonial) {
    setTimeout(() => {
        this.featuredTestimonial = testimonial;
    }, 200);
};
```
- testimonial link is hovered over, `updateFeaturedTestimonial()` is called
- wait 200 milliseconds before setting `featuredTestimonial` to the one hovered-over
- then, with `@mouseout` directive, call `clearTimer()` to clear the timeout when the mouse leaves the hover
