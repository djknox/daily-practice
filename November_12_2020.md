## Daily Practice
### Thursday, November 12th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Reusability: Part 2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/41)

Second approach from the previous lesson:  
- the accordion can be the full collection of all Questions + Answers/Title + Body/etc.

`Accordion.vue`
```
<template>
    <div>
        <accordion-item
            v-for="(item, index) in items"
            :key="index"
            :item="item"
            @toggled="onToggle"
            :is-open="item == openItem"
        >
        </accordion-item>
    </div>
</template>

<script>
    import AccordionItem from './AccordionItem.vue';

    export default {
        props: ['items'],

        components: { AccordionItem },

        data {
            return {
                activeItemIndex: null,
            };
        },

        methods: {
            onToggle (index) {
                if (this.activeItemIndex == index) {
                    return (this.activeItemIndex = null);
                }

                this.activeItemIndex = index;
            },
        }
    };
</script>
```


`AccordionItem.vue`
```
<template>
    <div>
        // ... markup to display item
    </div>
</template>

<script>
    export default {
        props: [
            'item',
            'isOpen'
        ],

        methods: {
            toggle () {
                this.$emit('toggled', this.$vnode.key);
            },
        }
    };
</script>
```
- `this.$vnode.key` is a way to access the item's `key` attribute
   * in this case, it is the item's index in the `items` array (passed to the `:key` prop)



Vue.js reminders:
- parent components communicate to child components through props
- child components communicate to parent components through events



## [Fixed to the Top](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/42)

Lesson: how to fix an HTML element to the top of the page, based upon the current scroll position using JavaScript
- [Modern CSS for Backend Developers: Make It Sticky](https://laracasts.com/series/modern-css-for-backend-developers/episodes/15) is a different lesson that shows how to do this with CSS
   * `position: sticky;`
      - hybrid between `position: fixed;` and `position: relative;`
      - normally displays as `position: relative;`, but will switch to `position: fixed;` when the element reaches a specified offset  
        Example:  
        ```
        div {
            position: sticky;
            top: 0;
        }
        ```


Scenario: a menu bar should fixed in its position, but stick to the top of the screen when the viewport is scrolled past it
`series-view.js`
```
import NotifyWhenUpdatedButton from '../components/lessons/NotifyWhenUpdatedButton';
import CompleteButton from '../components/lessons/CompleteButton';
import WatchLaterButton from '../components/lessons/WatchLaterButton';
import { throttle } from 'lodash';

export default {
    components: {
        NotifyWhenUpdatedButton,
        CompleteButton,
        WatchLaterButton,
    }

    mounted () {
        let banner = this.$refs['series-banner-meta'];
        let originalOffsetY = banner.offsetTop;
        
        window.addEventListener(
            'scroll',
            throttle(function () => {
                if (window.scrollY >= originalOffsetY) {
                    banner.classList.add('fixed', 'pin-t', 'w-full', 'z-10');
                }
                else {
                    banner.classList.remove('fixed', 'pin-t', 'w-full', 'z-10');
                }
            }, 100)
        );
    }
};
```
- Tailwind classes that pin an element to the top of the page: `fixed` + `pin-t`
- when the component is `mounted`, the vertical offset is grabbed from the banner element and a `scroll` event listener checks for when the screen is scrolled past the banner's vertical offset - when it is, the CSS classes to the pin the element to the top of the screen are added. when it is not, the CSS classes to pin the element to the top of the screen are removed
- the `scroll` event listener is limited to every 100 milliseconds using `lodash`'s `throttle()`



Chrome DevTools tip:
- in the Elements tab: click on an element, switch to the Console tab, and use `$0` to refer to that element

Vue.js tip:
- use [`ref`](https://vuejs.org/v2/api/#ref) attribute to directly access an element in JavaScript
- all `ref`s are stored in, and can be accessed through, `$refs`  
    Example:
    ```
    <!-- vm.$refs.p will be the DOM node -->
    <p ref="p">hello</p>

    <!-- vm.$refs.child will be the child component instance -->
    <child-component ref="child"></child-component>
    ```
- NOTE: `$refs` is only populated after the component has been rendered, so avoid accessing `$refs` from within templates or computed properties
