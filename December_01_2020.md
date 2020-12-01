## Daily Practice
### Tuesday, December 1st, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Reverse the Order](https://laracasts.com/series/modern-css-for-backend-developers/episodes/14)

This lesson demonstrates using flexbox's `flex-direction: column-reverse` to reverse the order of elements in a flex column - this can be useful for displaying content differently on different screen sizes.  



## [Make It Sticky](https://laracasts.com/series/modern-css-for-backend-developers/episodes/15)

Scenario: make a section's heading stick to the top of the page until the next section becomes visible  

CSS Attribute:  
- `position: sticky`
   * hybrid between `position: relative` and `position: fixed`
      - will normally display as `position: relative`
      - switches to `position: fixed` when reaching a specified offset

Example with specified offset:  
```
.sticky {
    position: sticky;
    top: 0;
}
```
- when the element reaches the `top: 0` offset, it will switch to `position: fixed`
