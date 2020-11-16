## Daily Practice
### Monday, November 16th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Render Functions: Part 1](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/45)

Usually stick with templates to declare HTML structures, but in some rare cases it makes sense to use render functions.

`ExampleComponent.vue`
```
<script>
    export default {
        render(createElement) {
            return createElement("h1", "Hello World");
        }
    };
</script>
```
...may also see:
```
<script>
    export default {
        render: h => h("h1", "Hello World")
    };
</script>
```
- `h` refers to [hyperscript](https://github.com/hyperhype/hyperscript): script that defines/describes HTML that should be constructed

another example:
```
<script>
    export default {
        render(element) {
            return element("h1", { class: ["one", "two"] }, "Hello World");
        }
    };
</script>
```
- can pass in attributes, props, etc. in the optional second parameter
- `element("h1", { class: ["one", "two"] }, "Hello World");` renders `<h1 class="one two">Hello World</h1>`

Nested elements:
```
<script>
    export default {
        data () {
            return {
                name: "John Doe",
            };
        },

        render(element) {
            return element("h1", { attrs: { id: "foobar" } }, [
                element("h1", this.name);
            ]);
        }
    };
</script>
```
- can pass in nested (child) elements in third parameter
- the above render function renders:
```
<div id="foobar">
    <h1>John Doe</h1>
</div>
```



## [Extract and Generalize: Infinite Scrolling Refactor](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/46)

This lesson shows a refactor and generalization of an infinite scrolling functionality on a page.
- move relevant code into its own component/module/etc. --> `LoadMore.js`
   * `scroll` event listener, method for determining if more results should be loaded, event emitting when more results should be loaded
- import `LoadMore` into whatever file needs to use it
