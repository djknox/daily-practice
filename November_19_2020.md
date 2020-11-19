## Daily Practice
### Thursday, November 19th, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Piece By Piece - Structure, Resets, and Flexbox](https://laracasts.com/series/modern-css-for-backend-developers/episodes/1)

Lesson involves simple styling of a navigation bar - review of flexbox, CSS selectors, etc.  



## [Flex Your Grids](https://laracasts.com/series/modern-css-for-backend-developers/episodes/2)

`flex` property:  
- property for the children of an element that has `display: flex`
- shorthand for `flex-grow`, `flex-shrink`, and `flex-basis` combined  

Example:
```
.flex {
    display: flex;
}
.flex > div {
    flex: 1;
}
.flex > a {
    flex: 2;
}
```
- all elements matching `.flex > div` will shrink/grow if there is less/more space than necessary
- elements matching `.flex > a` will shrink/grow by 2x as much as elements matching `.flex > div`

Adding `padding` on a flex item:  
- adding `padding` to the child of a `flex` element will change the alignment of the grid's columns
- to fix this, a common technique is to add negative margin to the `flex` element

Example:
```
.flex {
    display: flex;
    margin-right: -10px;
    margin-left: -10px;
}
.flex > div {
    padding: 10px;
}
```

`justify-content` is the inverse of `align-items`   
