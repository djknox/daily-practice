## Daily Practice
### Friday, October 23rd, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [This is Laravel Mix](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/23)

Simplifies interface for webpack configuration

`npm install laravel-mix`

Create a `webpack.mix.js` file for configuration  
`webpack.mix.js`
```
mix.js('resources/assets/js/app.js', 'public/js')
   .sass('resources/assets/sass/app.scss', 'public/css');
```
- compile everything in `resources/assets/js/app.js` to `public/js`
- compile everything in `resources/assets/sass/app.scss` to `public/css`

Add a flag in the `webpack` npm script in `package.json` to tell webpack to use the Laravel Mix's config file:  
`package.json`
```
"scripts": {
    "webpack": "... --config=node_modules/laravel-mix/setup/webpack.config.js",
}
```

If you need to change Laravel Mix's config file, simply copy and paste it to the project root:  
`cp node_modules/laravel-mix/setup/webpack.config.js ./`
- can now update the `webpack.config.js` file as usual

Laravel Mix Configuration Tips (these code examples are for `webpack.mix.js`):  
- create two different bundles by adding a second method call to `mix`
    ```
    mix.js('resources/assets/js/app.js', 'public/js')
       .js('resources/assets/js/forum.js', 'public/js')
       .sass('resources/assets/sass/app.scss', 'public/css');
    ```
    - creates `public/js/app.js` + `public/js/forum.js`  

- bundle two different files by adding them in an array to `mix`'s method call
    ```
    mix.js(['resources/assets/js/app.js', 'resources/assets/js/forum.js'], 'public/js')
       .sass('resources/assets/sass/app.scss', 'public/css');
    ```
    - compiles `app.js` and `forum.js` into single file at `public/js/app.js`

- add the `version` method to version/hash bundled files
    ```
    mix.js('resources/assets/js/app.js', 'public/js')
       .sass('resources/assets/sass/app.scss', 'public/css')
       .version();
    ```
    - creates `public/js/app.[long-hash].js` + `public/css/app.[long-hash].css`
    - made up example to illustrate: `public/js/app.2930i20kdafn4.js`



## [Shared State 101](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/24)

When multiple Vue instances/components need to share the same data, create a single source of truth for the shared data  
Example:
```
let store = {
    user: {
        name: 'John Doe',
    }
};

new Vue({
    el: '#one',

    data: {
        store
    }
});

new Vue({
    el: '#two',

    data: {
        store
    }
});
```
- both Vue instances use the `store` variable as a single source of truth
