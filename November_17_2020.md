## Daily Practice
### Tuesday, November 17th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Count Up](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/47)

Scenario: when the page is loaded, a component displays a number and counts  up rapidly to display a final number  

`index.blade.php`
```
<count :to=""></count>
```

`Count.vue`
```
<template>
    <span v-text="to"></span>
</template>

<script>
    export default {
        name: 'count',

        props: {
            to: {
                type: Number,
            }
        },

        data () {
            return {
                count: 0,
                increment: 1,
                interval: 0,
            };
        },

        mounted () {
            if (this.to > 1000) {
                this.increment = 50;
            }

            this.interval = setInterval(this.tick, 20);
        },

        methods: {
            tick () {
                if (this.count + this.increment >= this.to) {
                    this.count = this.to;
                    return clearInterval(this.interval);
                }

                return (this.count += this.increment);
            }
        },

        computed: {
            increment () {
                return Math.ceil(this.to / 20);
            },
        },
    };
</script>
```

__Next challenge__: numbers should only start counting up when the component is in the user's viewport (don't animate the counting until the component is on the screen)  

npm package: [`inViewport`](https://www.npmjs.com/package/in-viewport) - determine when an element is in the window viewport or a custom viewport  
- `npm install in-viewport`

`Count.vue`
```
<template>
    <span v-text="to"></span>
</template>

<script>
    import inView from 'in-viewport';

    export default {
        name: 'count',

        props: {
            to: {
                type: Number,
            }
        },

        data () {
            return {
                count: 0,
                interval: null,
            };
        },

        mounted () {
            inView(this.$el, () => {
                this.interval = setInterval(this.tick, 20);
            });
        },

        methods: {
            tick () {
                if (this.count + this.increment >= this.to) {
                    this.count = this.to;
                    return clearInterval(this.interval);
                }

                return (this.count += this.increment);
            }
        },

        computed: {
            increment () {
                return Math.ceil(this.to / 20);
            },
        },
    };
</script>
```
- in `mounted()`, only increment the `count` when the root element (`this.$el`) is in the viewport



## [Conditional Visibility and Vue Transitions](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/48)

Scenario: conditionally display an element based upon a different element being in the viewport  

`show.blade.php`:
```
<conditional-element
    when-hidden="#js-forum-reply-button"
>
    <div>
        ...
    </div>
</conditional-element>
```

`ConditionalElement.vue`:
```
<template>
    <div v-show="isVisible">
        <slot></slot>
    </div>

</template>

<script>
    import inView from 'in-viewport';

    export default {
        name: 'conditional-element',

        props: {
            whenHidden: {
                type: String,
            },
        },

        data () {
            return {
                isVisible: false,
            };
        },

        methods: {
            checkDisplay () {
                this.isVisible = !inView(document.querySelector(this.whenHidden));
            },
        },

        mounted () {
            window.addEventListener('scroll', this.checkDisplay, { passive: true })
        },
    };
</script>
```

__Next challenge__: add transitions when the `<conditional-element>` is displayed/hidden on the screen  

[Vue transitions](https://vuejs.org/v2/guide/transitions.html): specific CSS classes are added based on the visibility of the element wrapped within `<transition></transition>`  

`ConditionalElement.vue`:
```
<template>
    <transition name="fade">
        <div v-show="isVisible">
            <slot></slot>
        </div>
    </transition>
</template>

<script>
    import inView from 'in-viewport';

    export default {
        name: 'conditional-element',

        props: {
            whenHidden: {
                type: String,
            },
        },

        data () {
            return {
                isVisible: false,
            };
        },

        methods: {
            checkDisplay () {
                this.isVisible = !inView(document.querySelector(this.whenHidden));
            },
        },

        mounted () {
            window.addEventListener('scroll', this.checkDisplay, { passive: true })
        },
    };
</script>

<style>
    .fade-enter-active, .fade-leave-active {
        transition: opacity .3s;
    }

    .fade-enter, .fade-leave-to {
        opacity: 0;
    }
</style>
```
- the `fade` transition (with its corresponding CSS classes in the `<style>` tags) will be applied on the element that it encompasses when it is displayed/hidden


JavaScript tip:
- can pass `passive` as an optional third argument when adding an event listener
   * Example: `window.addEventListener('scroll', this.checkDisplay, { passive: true })`
- `{ passive: true }` makes the event listener passive, meaning it won't ever prevent the default action and allows browsers to use certain optimizations to improve performance



## [Two-Way Databinding Review](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/49)

Parent communicates to child through props  
Child communicates to parent through emitted events  

Example:  
`Join.vue` is parent of `PlanCards.vue`
- `PlanCards.vue` has a default `category` prop to display a particular set of cards
- `PlanCards.vue` emits a `change-category` event with a new `category` value when a new category is chosen
- `Join.vue` listens for the `change-category` event and updates the `category` prop back in to `PlanCards.vue` to display the chosen set of cards

`Join.vue`
```
<plan-cards
    :category="category"
    @change-category="category = $event"
>
    ...
</plan-cards>
```
- listen for the `change-category` event and update `category` with the new value
- pass `category` back in to `PlanCards` via the `category` prop

`PlanCards.vue`
```
changeCategory(category) {
    this.$emit('change-category', category);
}
```
- emit the `change-category` event with the updated `category` value when the category is changed


### This can be simplified using `sync`

`Join.vue`
```
<plan-cards
    :category.sync="category"
>
    ...
</plan-cards>
```
- `:category.sync="category"` does two things:
   * listen for the `change-category` event and update `category` with the new value
   * pass `category` back in to `PlanCards` via the `category` prop

`PlanCards.vue`
```
changeCategory(category) {
    this.$emit('update:category', category);
}
```
- the `update:category` event with the updated `category` value will allow the parent component to use `.sync` to both listen for the event and update the value
