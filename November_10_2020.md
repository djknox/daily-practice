## Daily Practice
### Tuesday, November 10th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Mega Menus and One-to-One Relationships](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/37)

Styles needed for a menu to drop down, on top of the rest of the page, without moving the content underneath it:
```
position: absolute;
width: 100%;
z-index: 10;
```

[PortalVue](https://github.com/LinusBorg/portal-vue)  
- a plugin to render DOM outside of a component
- a set of two components that allow you to render a component's template anywhere in the document

Example of `PortalVue`:
```
<portal to="destination">
  <p>This slot content will be rendered wherever the <portal-target> with name 'destination'
    is  located.</p>
</portal>

<portal-target name="destination">
  <!--
  This component can be located anywhere in your App.
  The slot content of the above portal component will be rendered here.
  -->
</portal-target>
```
- `<portal>` can send content to the corresponding `<portal-target>`, which is especially useful when `<portal-target>` is not in the same Vue component



## [Vue Portal Use Case #2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/37)

Notes on `PortalVue`:
- by default, the `<portal-target>` will be wrapped in a `<div class="vue-portal-target">` - this can be removed by adding the `slim` attribute to `<portal-target>`  
    Example:
    ```
    <portal-target name="destination" slim>
    </portal-target>
    ```
- when `<portal>`s are sending to multiple `<portal-target>` components with the same `name`, the last rendered `<portal-target>` will be the receiver
   * in order to avoid confusion, need to give `<portal-target>`s unique names  
        Example:
        ```
        <portal :to="`meta-links-${replyId}`">
        </portal>

        <portal-target name="meta-links-{{ $reply->id }}">
        </portal-target>
        ```
