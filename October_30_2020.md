## Daily Practice
### Friday, October 30th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Wrap Existing Tools into Components](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/34)

Carousel packages:
- [Flickity](https://flickity.metafizzy.co)
- [Glide.js](https://glidejs.com)

Installing `Flickity` via CDNs:  
Pull in styles:
```
<!-- CSS -->
<link rel="stylesheet" href="https://unpkg.com/flickity@2/dist/flickity.min.css">
```
Pull in JS:
```
<!-- JavaScript -->
<script src="https://unpkg.com/flickity@2/dist/flickity.pkgd.min.js"></script>
```
Initialize with HTML by adding `data-flickity` attribute to carousel div:
```
<div class="main-carousel" data-flickity='{ "cellAlign": "left", "contain": true }'>
```
Add items to the carousel div:
```
<div
    class="main-carousel"
    data-flickity='{ 
        "cellAlign": "left",
        "contain": true,
        "wrapAround": true 
    }'
>
    <img src="my-image.jpg">
    <img src="my-image2.jpg">
    <img src="my-image3.jpg">
    <img src="my-image4.jpg">
    <img src="my-image5.jpg">
</div>
```

**Goal**: extract the carousel into a `<carousel>` component that can accept items in a slot, like this:
```
<carousel>
    <img src="my-image.jpg">
    <img src="my-image2.jpg">
    <img src="my-image3.jpg">
    <img src="my-image4.jpg">
    <img src="my-image5.jpg">
</carousel>
```

Install `Flickity` via npm:
```
npm install flickity
```
Create `Carousel.vue`:
```
<template>
    <div>
        <slot></slot>
    </div>
</template>

<script>
    import Flickity from 'flickity';
    import 'flickity/dist/flickity.min.css';

    export default {
        name: 'carousel',

        props: {
            wraparound: {
                default: true,
            },
            autoplay: {
                default: false,
            },
        },

        mounted () {
            new Flickity(this.$el, {
                cellAlign: 'left',
                contain: 'true',
                wrapAround: this.wraparound,
                autoPlay: this.autoplay,
            })
        }
    };
</script>
```
- import `Flickity` package
- import `flickity.min.css` from `node_modules/flickity/dist`
- when the component is `mounted`, instantiate a new `Flickity` carousel on the root element (`this.$el`)

Register `Carousel.vue` in `app.js`:
```
Vue.component('carousel', require('./components/Carousel.vue').default);
```

Using `<carousel>` in a view:
```
<div id="app">
    <carousel
        :wraparound="true",
        :autoplay="true",
    >
        <img src="my-image.jpg">
        <img src="my-image2.jpg">
        <img src="my-image3.jpg">
        <img src="my-image4.jpg">
        <img src="my-image5.jpg"> 
    </carousel>
</div>
```
