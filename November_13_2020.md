## Daily Practice
### Friday, November 13th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Fixed to the Top: Part 2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/43)

Challenge: generalize component from previous lesson  

`Pinned.vue`
```
<template>
    <div>
        <slot>
        </slot>
    </div>
</template>

<script>
    export default {
        mounted () {
            let element = this.$el;
            let originalOffsetY = element.offsetTop;
            
            window.addEventListener(
                'scroll',
                throttle(function () => {
                    if (window.scrollY >= originalOffsetY) {
                        element.classList.add('fixed', 'pin-t', 'w-full', 'z-10');
                    }
                    else {
                        element.classList.remove('fixed', 'pin-t', 'w-full', 'z-10');
                    }
                }, 100)
            );
        },
    };
</script>
```


JavaScript tip:  
these two code blocks do the same thing:  
```
if (window.scrollY >= originalOffsetY) {
    element.classList.add('fixed', 'pin-t', 'w-full', 'z-10');
}
else {
    element.classList.remove('fixed', 'pin-t', 'w-full', 'z-10');
}
```
```
el.classList[
    window.scrollY >= originalOffsetY ? 'add' : 'remove'
]('fixed', 'pin-t', 'w-full', 'z-10');
```
- on any object, you can call a property using dot notation (`element.classList.add()`) or bracket notation (`element.classList['add']()`)



## [Tooltips Three Ways](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/44)

Create a standard tooltip using three techniques:  
1. data attributes
2. Vue directives
3. dedicated Vue components


Package: [Popper](https://popper.js.org/)  
- __NOTE__: for this lesson, an outdated package called `tooltip.js` (built on top of Popper) was used

### Data Attributes

`welcome.blade.php`
```
<div id="app">
    <p>
        Hello there,
        <span
            data-tooltip="I am a tooltip!"
            data-tooltip-placement="top"
        >
            hover over me
        </span>.
    </p>
</div>
```

`app.js`
```
const app = new Vue({
    el: '#app',

    mounted () {
        document.querySelectorAll('[data-tooltip]').forEach(element => {
            new Tooltip(element, {
                placement: element.dataset.tooltipPlacement || 'top',
                title: element.dataset.tooltip,
            })
        });
    }
});
```
- when the `#app` element is `mounted`, find all the elements with the `data-tooltip` attribute, loop over them and create a new `Tooltip` for each element



### Vue Directives

Vue docs - [Custom Directives](https://vuejs.org/v2/guide/custom-directive.html)

`welcome.blade.php`
```
<div id="app">
    <p>
        Hello there,
        <span
            v-tooltip:top="'I am a tooltip!'"
        >
            hover over me
        </span>.
    </p>
</div>
```

`app.js`
```
Vue.directive('tooltip', {
    bind (element, bindings) {
        new Tooltip(element, {
            placement: bindings.arg || 'top',
            title: bindings.value,
        })
    }
});

const app = new Vue({
    el: '#app',
});
```
- custom `tooltip` Vue directive:
   * elements with the `v-tooltip` directive will have the tooltip created on them
   * `bindings` is an object that contains the various properties
      - `v-tooltip` - `tooltip` is the `name` property (referenced by `bindings.name`)
      - `v-tooltip="1 + 1"` - `2` is the `value` property (referenced by `bindings.value`)
      - `v-tooltip="1 + 1"` - `"1 + 1"` is the `expression` property (referenced by `bindings.expression`)
      - `v-tooltip:top` - `top` is being used here as an argument (referenced by `bindings.arg`)
      - `v-tooltip.top` - `top` is being used here as a modifier (referenced by `bindings.modifiers.top`)



### Dedicated Vue Components

`welcome.blade.php`
```
<div id="app">
    <p>
        Hello there,
        <span
            data-tooltip-name="our-products-tooltip"
        >
            learn about our products.
        </span>.

        <tooltip name="our-products-tooltip">
            <h1>Our Products</h1>

            <p>
                Our products include...
            </p>
        </tooltip>
    </p>
</div>
```

`app.js`
```
import Tooltip from './components/Tooltip.vue';
Vue.component('tooltip', Tooltip);

const app = new Vue({
    el: '#app',
});
```

`Tooltip.vue`
```
<template>
    <div v-show="false">
        <slot>
        </slot>
    </div>
</template>

<script>
    import PopperTooltip from 'tooltip.js';

    export default {
        props: {
            name: {

            },
        },

        mounted () {
            document.querySelectorAll(`[data-tooltip-name=${this.name}]`).forEach(element => {
                new PopperTooltip(element, {
                    placement: 'top',
                    title: this.$el.innerHTML,
                    html: true,
                });
            });
        }

    };
</script>
```
- when `Tooltip` component is mounted, look in the DOM (using `document.querySelectorAll()`) to find elements with the `data-tooltip-name` attribute that matches the `name` prop passed to it
- loop through each of the elements, instantiating a tooltip for each one
   * the tooltip's `title` property is taken from the inner HTML of the root element
   * `html: true` tells Popper to render HTML
- NOTE: the root element has `v-show="false"` so that it does not display except for in the created tooltip
   * a render function will handle this more elegantly, but will be saved for the next lesson
