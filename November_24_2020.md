## Daily Practice
### Tuesday, November 24th, 2020
---


# [Modern CSS for Backend Developers](https://laracasts.com/series/modern-css-for-backend-developers)


## [Tailwind Customization](https://laracasts.com/series/modern-css-for-backend-developers/episodes/8)

`npm install tailwindcss`
- puts `"tailwindcss"` as a dependency in `package.json`
- creates a `node_modules/.bin/tailwind` executable
   * can be used for creating the default tailwind configuration file (`tailwind.js`)
      - `node_modules/.bin/tailwind init tailwind.js`
      - __NOTE__: this has changed since this lesson was created - a `tailwind.config.js` file is created that contains much less configuration information. to get the full configuration settings, run `npm tailwind init --full`  



## [Call to Action Banner Design](https://laracasts.com/series/modern-css-for-backend-developers/episodes/9)

This lesson involves taking a mock-up design for a banner and implementing it in HTML + CSS.  



## [Mobile-First Design](https://laracasts.com/series/modern-css-for-backend-developers/episodes/10)

Design in terms of the mobile layout, and then build to larger sizes using breakpoints  

Breakpoints: styles to be applied when the screen reaches a particular __minimum width__  
Example using Tailwind breakpoints:  
- `<div class="md:w-1/2 lg:w-1/4"></div>`
   * the `div` should be `w-1/2` when the screen is `md` size and larger  
   * the `div` should be `w-1/4` when the screen is `lg` size and larger  
