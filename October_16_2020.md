## Daily Practice
### Friday, October 16th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Practical Component Exercise #3: Tabs](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/11)

index.html:
```
<!DOCTYPE html>
<html>
<head>
    <title></title>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.2.3/css/bulma.css">
    
    <style>
        body { padding-top: 40px; }
    </style>
</head>
<body>
    <div id="root" class="container">
        <tabs>
            <tab name="About Us" :selected="true">
                <h1>Here is the content for the about us tab.</h1>
            </tab>

            <tab name="About Our Culture">
                <h1>Here is the content for the about our culture tab.</h1>
            </tab>

            <tab name="About Our Vision">
                <h1>Here is the content for the about our vision tab.</h1>
            </tab>
        </tabs>
    </div>

    <script src="https://unpkg.com/vue@2.1.3/dist/vue.js"></script>
    <script src="main.js"></script>
</body>
</html>
```
- <tabs> component with <tab> components slotted in


main.js:
```
Vue.component('tabs', {
    template: `
        <div>
            <div class="tabs">
                <ul>
                    <li v-for="tab in tabs" :class="{ 'is-active': tab.isActive }">
                        <a :href="tab.href" @click="selectTab(tab)">{{ tab.name }}</a>
                    </li>
                </ul>
            </div>
            <div class="tabs-details">
                <slot></slot>
            </div>
        </div>
    `,

    data() {
        return { tabs: [] };
    },

    created() {
        this.tabs = this.$children;
    },

    methods: {
        selectTab(selectedTab) {
            this.tabs.forEach(tab => {
                tab.isActive = (tab.href == selectedTab.href);
            });
        }
    }
});


Vue.component('tab', {
    template: `
        <div v-show="isActive"><slot></slot></div>
    `,

    props: {
        name: { required: true },
        selected: { default: false }
    },

    data() {
        return {
            isActive: false
        };
    },

    computed: {
        href() {
            return '#' + this.name.toLowerCase().replace(/ /g, '-');
        }
    },

    mounted() {
        this.isActive = this.selected;
    },
});

new Vue({
    el: '#root'
});
```



## [Named Slots in a Nutshell](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/14)

Default slot:
```
<slot></slot>
```
- only works for a single, default slot

Named slot in `my-vue-component`'s template:
```
<div>
    <slot name="first-slot">
    <slot>

    <slot name="second-slot">
    <slot>
</div>
```

Using the slot in another file:
```
<my-vue-component>
    <template v-slot:first-slot>
        <div>
            This is the first slot's content.
        </div>
    </template>

    <template v-slot:second-slot>
        <div>
            This is the second slot's content.
        </div>
    </template>
</my-vue-component>
```
- use `v-slot` directive on a `<template>`
- provide the name of the slot as the argument

Rendered HTML:
```
<div>
    <div>
        This is the first slot's content.
    </div>

    <div>
        This is the second slot's content.
    </div>
</div>
```



## [Webpack and vue-cli](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/16)

[Webpack](https://webpack.github.io/)  
[vue-cli](https://github.com/vuejs/vue-cli)  

loaders handle pre-processing to anything that is required in (when you import, require, load, etc. a file)
- use Sass, load SVGs, etc.

`vue-loader`
- a loader for Webpack that can transform single file Vue components into plain JavaScript modules

Some examples of configuration in `webpack.config.js`:
- `entry`: the entry-point to the application
   * example: `entry: './src/main.js'`
- `output`: when everything is compiled down, where it should go
   * example:
        ```
        output: {
            path: path.resolve(__dirname, './dist'),
            publicPath: '/dist/',
            filename: 'build.js'
        }
        ```
- `module`: the various loaders
   * example:
        ```
        module: {
            rules: [
                {
                    test: /\.vue$/,
                    loader: 'vue-loader',
                    options: {
                        // vue-loader options go here
                    }
                },
                {
                    test: /\.js$/,
                    loader: 'babel-loader',
                    exclude: /node-modules/
                },
                {
                    test: /\.(png|jpg|gif|svg)$/,
                    loader: 'file-loader',
                    options: {
                        name: '[name].[ext]?[hash]'
                    }
                },
            ]
        }
        ```
        - look for any `.vue` files and apply the `vue-loader`
        - look for any `.js` files and apply the `babel-loader`
        - look for any `.png`, `.jpg`, `.gif`, `.svg` files and apply the `file-loader`

No need to memorize, or completely understand, the `webpack` file
