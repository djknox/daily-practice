## Daily Practice
### Thursday, October 8th, 2020
---


# [Blade Component Cookbook](https://laracasts.com/series/blade-component-cookbook)


## [Mobile Nav Link Component](https://laracasts.com/series/blade-component-cookbook/episodes/8)

Duplication can be a good thing when it comes to responsive design.
- one design/component/etc. for desktop/larger screens
- separate design/component/etc. for mobile/smaller screens

Start with mobile design first  

Icons: [Zondicons](https://www.zondicons.com/icons.html)  

Example of hiding/displaying an element based on screen size:
```
<nav class="hidden md:flex">
</nav>
```
- `hidden` display until screen size reaches the `md` breakpoint and becomes `flex` display



## [Tabs Component](https://laracasts.com/series/blade-component-cookbook/episodes/9)

__Note__: These examples are using [Alpine.js](https://github.com/alpinejs/alpine).

```
<x-layout>
    <x-section>
        <div x-data="{ active: 'First' }">
            <div>
                <button @click="active = 'First'">First</button>
                <button @click="active = 'Second'">Second</button>
                <button @click="active = 'Third'">Third</button>
            </div>

            <div>
                <div x-show="active === 'First'">First content goes here</div>
                <div x-show="active === 'Second'">Second content goes here</div>
                <div x-show="active === 'Third'">Third content goes here</div>
            </div>
        </div>
    </x-section>
</x-layout>
```

Using Blade components:
```
<x-layout>
    <x-section>
        <x-tabs active="First">
            <x-tab name="First">
                First content goes here
            </x-tab>
            <x-tab name="Second">
                Second content goes here
            </x-tab>
            <x-tab name="Third">
                Third content goes here
            </x-tab>
        </x-tabs>
    </x-section>
</x-layout>
```

`tabs.blade.php`:
```
@props(['active'])

<div
    x-data="{
        activeTab: '{{ $active }}',
        tabHeadings: [],
        toggleTabs() {
            this.tabs.forEach(
                tab => tab.__x.$data.showIfActive(this.activeTab);
            );
        }
    }"
    x-init="() => {
        tabs = [...$refs.tabs.children];

        tabHeadings = tabs.map(tab => tab.__x.$data.name);

        toggleTabs();

    }">
    <div>
        <template
            x-for="(tab, index) in tabHeadings"
            :key="index">
            <button
                x-text="tab"
                @click="
                    activeTab = tab;
                    toggleTabs();
                "
                :class="tab === activeTab ? 'bg-blue-500 text-white' : 'text-gray-800'">
            </button>
        </template>
    </div>

    <div x-ref="tabs">
        {{ $slot }}
    </div>
</div>
```
- `$el` - variable for the root element
- `$el.children` is an instance of `HTMLCollection`, but can make it an array by destructuring with `...` and putting into an array: `[...$el.children]`
- `__x` is used by Alpine.js as a way to access the component instance (and from there can grab data with `$data`, etc.)
- `x-ref` attribute is used by Alpine.js as a way to reference a specific element
   * with `$refs.tabs`, can access the element with `x-ref="tabs"`
   * with `$refs.anotherRefName`, can access the element with `x-ref="anotherRefName"`
   * in the example above (`$refs.tabs.children`), this helps grab the children of the `tabs` element rather than from the root element (which was originally being done with `$el.children`)
- `x-init` runs when the component is initialized
- `toggleTabs()`: for each tab, set the `show` property to `true`/`false` based on the comparison of the tab's name with the current active tab

`tab.blade.php`:
```
@props(['name'])

<div
    x-data="{
        name: '{{ $name }}',
        show: false,
        showIfActive(active) {
            this.show = (this.name === active);
        }
    }"
    x-show="show">
    {{ $slot }}
</div>
```


Next step - need to inform child `x-tab` components when the active tab has changed:
- could dispatch an event with `$dispatch()`
   * not always the best choice, because a global event will be dispatched and this can cause issues with other elements (ex: other instances of `x-tab` components on the page)
- since `x-tabs` and `x-tab` are related and will not exist independent of each other, this example just passes around the active tab rather than dispatching a global event
