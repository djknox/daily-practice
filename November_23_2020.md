## Daily Practice
### Monday, November 23rd, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Workshop - FAQs](https://laracasts.com/series/modern-css-for-backend-developers/episodes/6)

Lesson scenario: create an FAQ section  

Related lessons:
- [Reusability](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/40)
- [Reusability: Part 2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/41)


Vue tip:
- can use `v-if`, `v-show`, etc. on a `<template>` tag  
    Example:  
    ```
    <template v-show="isOpen">
        <svg ...>
        </svg>
    </template>
    ```



## [Pricing Selection Design](https://laracasts.com/series/modern-css-for-backend-developers/episodes/7)

Lessons scenario: style a pricing menu/grid  

Sass tip:
- can use `@apply` keyword to make one class also apply other classes  
    Example:  
    ```
    .btn {
        @apply .bg-blue .text-white .no-underline;
    }
    ```

General CSS tip:
- when trying to align text, try adjusting the `line-height`
   * Tailwind: `leading-none`, `leading-tight`, etc.
