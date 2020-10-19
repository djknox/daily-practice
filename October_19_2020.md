## Daily Practice
### Monday, October 19th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Hot Module Replacement](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/17)

Hot Module Replacement is a feature of `vue-loader`

After initializing a new project with Vue CLI -  
`package.json`: `npm run dev` script is an alias for:  
`cross-env NODE_ENV=development weback-dev-server --open --inline --hot`
- `--hot` flag denotes hot module replacement

Hot Module Replacement will swap out components without losing data
- make changes to a component and will not need to refresh the page
- can change the content of a component and data will still be saved
