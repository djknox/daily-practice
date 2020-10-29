## Daily Practice
### Thursday, October 29th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Vue Component Responsibility](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/31)

When naming things with compound words (examples: `toggleFavorite()`, `favoriteClasses`, `favoritesCount`, etc.), this might be an indication that a new component could be pulled out.  
- example: create a `Favorite` component and compound words become simplified to `toggle()`, `classes`, `count`, etc.

Vue tip:
- can add the `inline-template` attribute on a child component to use its inner content as its template
   * in a Laravel project, this allows the use of Blade/PHP in the template for a Vue component  
   Example:
        ```
        <my-component inline-template>
            <div>
                <p>These are compiled as the component's own template.</p>
                <p>Not parent's transclusion content.</p>
            </div>
        </my-component>
        ```



## [Vue Subclassing](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/32)

Lesson example:  
Collection pattern - having a single representation of something and housing multiple instances of them in a single place
- example:
   * `<Replies>` with many `<Reply>` components inside
   * `<Replies>` can listen when a new `<Reply>` is added/removed and re-renders

Creating a generic `Collection.js` class:
```
export default Vue.extend({
    props: ['data'],

    data () {
        return {
            items: this.data,
        };
    },

    methods: {
        add (item) {
            this.items.push(item);

            this.$emit('added');
        },

        remove (index) {
            this.items.splice(index, 1);

            this.$emit('removed');
        },
    },
});
```
- has data passed to it via the `data` prop
- has methods for adding + removing a data item


Vue tip:
- can extend a Vue component with `.extend()` or `extends`
   * example:
        ```
        import Collection from '../Collection';

        export default Collection.extend({
            ...
        });
        ```
   * Single file components use the `extends` option:
        ```
        var CompA = { ... }

        // extend CompA without having to call `Vue.extend` on either
        var CompB = {
            extends: CompA,

            ...
        }
        ```



## [Scoped Slots](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/33)

Template scoping is good for when you need to override the markup for a given slot within a parent but don't have access to the relevant underlying data.  

Can pass properties to slots:  
`MenuList.vue`
```
<li v-for="item in items">
    <slot :item="item"></slot>
</li>
```

To access the properties:  
`welcome.blade.php`
```
<menu-list :items="['one', 'two', 'three']">
    <template v-slot:default="props">
        <h2 v-text="props.item"></h2>
    </template>
</menu-list>
```
- use `v-slot` with a value to define a name for all the props given to the slot (in the example above: `v-slot:default="props"` says that `props` will hold all the props given to the `default` slot)
- access the child component's props by referencing them from the name given for all the slot props (in the example above: `props.item`)
