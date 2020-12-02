## Daily Practice
### Wednesday, December 2nd, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Wield Complete Control Over the Order](https://laracasts.com/series/modern-css-for-backend-developers/episodes/16)

Flexbox ordering  
Specify the order of elements within a flex element by using the `order` property
- default value for elements is 0



## [Modals with Zero JavaScript](https://laracasts.com/series/modern-css-for-backend-developers/episodes/17)

[CSS Pseudo-classes](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)  
- keywords added to a selector that specify a special state of the selected element(s)
- examples: `:hover`, `:visited`, `:checked`  

[`:target`](https://developer.mozilla.org/en-US/docs/Web/CSS/:target)  
- pseudo-class that represents a unique element (the target element) with an `id` matching the URL's fragment  



```
<style>
    .overlay {
        visibility: hidden;
    }

    .overlay:target {
        visibility: visible;
    }
</style>


...


<a href="#join-modal">Join</a>

<div 
    id="join"
    class="overlay"
>
    <div class="modal">
        ...

        <a href="#">X</a>
    </div>
</div>
```

- by default, hide the `.overlay` (`visibility: hidden`)  
- when `#join` becomes the `target` by clicking on the `Join` anchor tag, `.overlay` also becomes the `target`
- display the `.overlay` when it is the `:target` (`visibility: visible`)
- when `#` becomes the `target` by clicking the `X` anchor tag, `.overlay` is hidden because `#join` is no longer the `target`
