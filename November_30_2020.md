## Daily Practice
### Monday, November 30th, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Mobile-First Layouts](https://laracasts.com/series/modern-css-for-backend-developers/episodes/11)

This lesson demonstrates using flexbox and tailwind to create:
- sticky footer
- different layout for mobile vs larger screen sizes using tailwind breakpoints 

Default styles should be for mobile layouts, while styles for larger screens are applied with breakpoints  



## [Piece By Piece Mobile Tweaking](https://laracasts.com/series/modern-css-for-backend-developers/episodes/12)

Tailwind tip - `@screen` directive:  
- can create media queries that reference Tailwind breakpoints by name  
    Example (using a Tailwind breakpoint named `sm` at 640px):  
    ```
    @media (min-width: 640px) {
    /* ... */
    }
    ```

    can be written as:  
    ```
    @screen sm {
    /* ... */
    }
    ```



## [Better Sizing and Spacing With rems](https://laracasts.com/series/modern-css-for-backend-developers/episodes/13)

`rem` = "root `em`"
- specify the size of an element based on the size of the root element (`<html>`)  

Converting pixels to `rem`s:  
- divide pixel value of target element by pixel value of root element to get `rem` value  
    Example:
       * `html { font-size: 16px; }`
       * `h1 { font-size: 40px; }`
       * Calculating the `font-size` of `h1` in `rem`s: 40px / 16px = 2.5rem

Can size everything using `rem` units, and then adjust the size of the root element  
Example:  
```
html {
    font-size: 12px;
}

@screen lg {
    html {
        font-size: 16px;
    }
}
```  
- set default `font-size` of `12px`, and increase to `16px` for `lg` screens
- can also put this on the `<html>` tag itself using Tailwind: `<html class="text-xs lg:text-base">`
