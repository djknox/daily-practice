## Daily Practice
### Friday, November 20th, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Workshop - Card Design](https://laracasts.com/series/modern-css-for-backend-developers/episodes/3)

Using `alpha` transparency to subtly color elements that sit on top of colored backgrounds:  
```
.card {
    background: linear-gradient(to bottom, #21c8f6, #637bff);
}
.card-button {
    background: rgba(0, 0, 0, 0.4);
}
```
- `.card-button` elements will be black with a 40% transparency
- the colored background (`.card`) will bleed through a bit, giving `.card-button` some of its color



## [Refactoring to Utility Classes and Tailwind](https://laracasts.com/series/modern-css-for-backend-developers/episodes/4)

This lesson involes refactoring the previous lesson using [`TailwindCSS`](https://tailwindcss.com/) utility classes  



## [Easy Flexbox Wins](https://laracasts.com/series/modern-css-for-backend-developers/episodes/5)

Examples of where flexbox can be used:  

### Navigation

Distribute the nav items equally across the width of the nav bar  

```
<nav class="bg-blue-lightest py-4 px-4">
    <a href="#">Home</a>
    <a href="#">About</a>
    <a href="#">Our Mission</a>
    <a href="#">Contact</a>
</nav>
```

Add `flex` and `justify-content`:  
```
<nav class="bg-blue-lightest py-4 px-4 flex justify-content">
    <a href="#">Home</a>
    <a href="#">About</a>
    <a href="#">Our Mission</a>
    <a href="#">Contact</a>
</nav>
```


Distribute some of the nav items equally across the left side and some across the left side of the nav bar  

```
<nav class="bg-blue-lightest py-4 px-4">
    <a href="#">Home</a>
    <a href="#">About</a>
    <a href="#">Our Mission</a>
    <a href="#">Contact</a>
</nav>
```

Add `flex` and `justify-between`, separate nav items into `<div>`s:
```
<nav class="bg-blue-lightest py-4 px-4 flex justify-between">
    <div>
        <a href="#">Home</a>
        <a href="#">About</a>
    </div>
    
    <div>
        <a href="#">Our Mission</a>
        <a href="#">Contact</a>
    </div>
</nav>
```

### Images + Text

Align an image with text (image on left, text centered vertically on right, both items centered horizontally)  

```
<div>
    <img src=".../image.png">

    <div>
        <h3>My Trip to...</h3>

        <p>
            Lorem ipsum dolor sit amet, ...
        </p>
    </div>
</div>
```


Add `flex` and `items-center`:  
```
<div class="flex items-center">
    <img src=".../image.png">

    <div>
        <h3>My Trip to...</h3>

        <p>
            Lorem ipsum dolor sit amet, ...
        </p>
    </div>
</div>
```

### Text

Centered text  

```
<div>
    <div>
        <h3>Flexbox is Amazing</h3>

        <p>
            Lorem ipsum dolor sit amet, ...
        </p>
    </div>
</div>
```


Add `flex`, `items-center`, `justify-center`, height and width values to parent container:
```
<div class="h-16 w-16 flex items-center">
    <div>
        <h3>Flexbox is Amazing</h3>

        <p>
            Lorem ipsum dolor sit amet, ...
        </p>
    </div>
</div>
```

### Sticky Footer

Footer that stays at the bottom of the screen  

```
<div id="root">
    <header>
        <h1>My Website</h1>
    </header>

    <main>
    </main>

    <footer>
    </footer>
</div>
```


Add the following utility classes:  
```
<div
    id="root"
    class="flex flex-col min-h-screen">
    <header>
        <h1>My Website</h1>
    </header>

    <main class="flex-1">
    </main>

    <footer>
    </footer>
</div>
```
- `min-h-screen` sets `min-height: 100vh`
   * sets the element to at least 100% of the viewport (screen)  

1. set root container to `flex` and `flex-col`
2. set root container height to 100% of the viewport (at its smallest)
3. set main container to `flex-1` so that it will grow/shrink
4. footer will always be pushed to the bottom because the main container will grow/shrink if necessary
