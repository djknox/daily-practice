## Daily Practice
### Thursday, October 22nd, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Webpack Config From Scratch](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/22)

From the previous lessons, the classes (`Error` and `Form`) have been contained in the same `app.js` file as everything else - it would be convenient to have them in their own modules. Browsers do not support modules yet, so have to use a module bundler like Browserify or Webpack.  

`npm install webpack`
- executable at `node_modules/.bin/webpack`
   * `node_modules/.bin/webpack --hide-modules` will compile everything and will not display each module in the results
   * `node_modules/.bin/webpack --watch` will compile everything and re-compile when there are changes
- create npm scripts with these commands by adding them to the `"scripts"` object in `package.json`
    ```
    "scripts": {
        "webpack": "export NODE_ENV=development && webpack --hide-modules",
        "dev": "export NODE_ENV=development && webpack --watch",
        "production": "export NODE_ENV=production && webpack --hide-modules",
    }
    ```
    - can run `npm run webpack`, `npm run dev`, `npm run production`, etc.

`webpack.config.js`
```
let webpack = require('webpack');

let path = require('path');


module.exports = {
    entry: './resources/assets/js/app.js',

    output: {
        path: path.resolve(__dirname, 'public/js'),
        filename: 'app.js',
        publicPath: './public'
    },
};
```
- starting at `./resources/assets/js/app.js` compile everything to `public/js/app.js`
- can now put `Form` class in its own `Form.js` file
   * `import Form from './Form'` in any components using it
- can now put `Errors` class in its own `Errors` file
   * `import Errors from './Errors.js'` in any components using it

For modules that would be useful in every part of the project, add to the `window` object from main `app.js` file:
- example: `window.axios = axios;`
- example: `window.Form = Form;`

From Vue docs:  
by default, the npm package exports the runtime-only build - this can cause errors such as:  
`[Vue warn]: Failed to mount component: template or render function not defined.`  
- the standalone/full build includes the compiler and supports the `template` option  

To use the standalone/full build:  
`webpack.config.js`
```
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.common.js',
        }
    },
```

Minifying bundled code for production:  
`webpack.config.js`
```
if (process.env.NODE_ENV === 'production') {
    module.exports.plugins.push(
        new webpack.optimize.UglifyJsPlugin({
            sourcemap: true,
            compress: {
                warnings: false
            }
        })
    );
}
```

Registering `babel-loader` to compile ES2015 code into vanilla JavaScript:  
`webpack.config.js`
```
module: {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
        }
    ]
},
```
- reg ex `/\.js$/` will be used to find files, excluding those in `/node_modules/`, and load using `babel-loader`
- also need to
   * `npm install babel-loader`
   * `npm install babel-core`
   * `npm install babel-preset-es2015`
   * create `.babelrc` config file:
        ```
        {
            "presets": [
                ["es2015", { 'modules': false }]
            ]
        }
        ```


Disable any vue warnings, etc. when in production:  
`webpack.config.js`
```
if (process.env.NODE_ENV === 'production') {
    // UglifyJsPlugin code for minification...


    module.exports.plugins.push(
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: 'production'
            }
        })
    );
}
```
- tracks down any references to `process.env.NODE_ENV` and sets to 'production'

Creating one bundle for application code and a separate bundle for vendor libraries
- when application code is updated and re-compiled, the vendor library code can stay cached
add to `entry` object in `webpack.config.js`
```
entry: {
    app: './resources/assets/js/app.js',
    vendor: ['vue', 'axios']
},
```

add `CommonsChunkPlugin` to `plugins` array in `webpack.config.js`
```
plugins: [
    new webpack.optiize.CommonsChunkPlugin({
        names: ['vendor']
    })
]
```
- `names` corresponds to the `vendor` key given in `entry` object for all the vendor libraries
- will create a `vendor.js` file containing only the vendor library code

change `filename` in `output` object to be dynamic  
`webpack.config.js`
```
output: {
    path: path.resolve(__dirname, 'public/js'),
    filename: '[name].js',
    publicPath: './public'
},
```
- webpack will detect `[name]` and replace with the appropriate name, such as `app` or `vendor`


Tips:
- `path` is a node module that provides utilities for working with file and directory paths
   * example: `path.resolve()` will resolve to an absolute path
