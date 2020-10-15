## Daily Practice
### Thursday, October 15th, 2020
---


# [Refactoring Workshops](https://laracasts.com/series/refactoring-workshops)


## [Improve Confusing Code With Small Refactors](https://laracasts.com/series/refactoring-workshops/episodes/1)

`mix.combine()`
- `laravel-mix/src/components/Combine.js`
- provide an array of files/wildcards and they will be merged and saved to an output file (also minified in production)  

example:
```
mix.combine(
    ['public/libs/*.css', 'public/stylesheets/*.css'],
    'public/css/all.css'
);
```

`glob`
- recursively gets all the files in a directory
- in `mix.combine()`, it is good for accepting a wildcard and scanning the directory for those files


Refactoring Tips:
- always have tests to ensure that breaking changes don't occur during refactoring  
- look for repeated code
   * consolidate into method(s) to simplify
- look for conditionals to reduce
   * the more indentation in your code, the more complex it (usually) is
