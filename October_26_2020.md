## Daily Practice
### Monday, October 26th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Custom Input Components](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/25)

`v-model` is syntactic sugar:  
```
<input 
    v-model="coupon"
    type="text"
>
```
is the same as:
```
<input
    :value="coupon"
    @input="coupon = $event.target.value"
    type="text"
>
```

`v-model` on a component  
In the HTML view:
```
<coupon v-model="coupon"></coupon>
```

`<coupon>` component:
```
<template>
    <div>
        <input 
            :value="code"
            @input="updateCode($event.target.value)"
        >
    </div>
</template>

<script>
    export default {
        props: {
            'code',
        },

        methods: {
            updateCode (code) {
                this.$emit('input', code);
            },
        },
    };
</script>
```
- `bind` the input to the component's prop
- `emit` the updated prop
- `v-model` will handle the parent component binding


Vue.js tips:  
- use `ref` on an element to give it an ID to use by accessing it through `$refs` 
example:  
    ```
    // add ref to the element
    <input type="text" :value="coupon" ref="input">

    ...

    // accessing the element to update its value
    this.$refs.input.value = 'updated value';
    ```



## [Vue SPA Essentials: Routing](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/26)

`npm install vue-router`

Create `routes.js` file for all routing:
```
import VueRouter from vue-router;

let routes = [
    {
        path: '/',
        component: require('views/Home.vue')
    },
    {
        path: '/about',
        component: require('views/About.vue')
    },
];

new VueRouter({
    routes: routes
});
```
- each route declaration has a `path` and `component`
   * `path`: the URI to match
   * `component`: the component that corresponds to the route URI

import `routes.js` into root Vue instance:
```
import router from './routes';

new Vue({
    router: router,
});
```

in Laravel's `bootstrap.js` file (or `resources/app.js`):
```
import VueRouter from 'vue-router';
Vue.use(VueRouter);
```

Using the `<router-link>` component in HTML views:  
`<router-link to="/">Home</router-link>`  
- `<router-link>` creates the anchor tag
- a `router-link-active` CSS class will be dynamically added when the link is matched (will need to add your own styling for this class)
   * add `exact` attribute to ensure that matches to the URI are exactly as they should be
    - without `exact` attribute: `<router-link to="/">Home</router-link>`
       * will add `router-link-active` to "/", as well as "/about", "/resources", etc. (because of the leading backslash)
    - with `exact` attribute: `<router-link to="/" exact>Home</router-link>`
       * will add `router-link-active` to "/", but not "/about", "/resources", etc.
   * can override the CSS class name that is added in `routes.js` by setting a `linkActiveClass` property:
        ```
        export default new VueRouter({
            routes,
            linkActiveClass: 'is-active'
        });
        ```

Using the `<router-view>` component in HTML views:  
`<router-view></router-view>`  
- `<router-view>` displays the corresponding component
