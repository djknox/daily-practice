## Daily Practice
### Wednesday, October 7th, 2020
---


# [Blade Component Cookbook](https://laracasts.com/series/blade-component-cookbook)


## [Modal Component](https://laracasts.com/series/blade-component-cookbook/episodes/5)

```
<x-layout>
    <x-modal
        title="Deactivate account?"
        submit-label="Deactivate">
        <x-slot name="trigger">
            <button @click="on = true">
                Show Modal
            </button>
        </x-slot>
        Are you sure you want to deactivate your account?
    </x-modal>
<x-layout>
```

The code used for the `x-modal` component was taken from [TailwindUI's example modals](https://tailwindui.com/components/application-ui/overlays/modals).  


## [Dropdown Component](https://laracasts.com/series/blade-component-cookbook/episodes/6)

__Note__: This example is using [Alpine.js](https://github.com/alpinejs/alpine).
```
<x-layout>
    <x-dropdown>
        <x-slot name="trigger">
            <button>
                Click Me
            </button>
        </x-slot>

        <x-dropdown-link href="/">
            One
        </x-dropdown-link>
        <x-dropdown-link href="/">
            Two
        </x-dropdown-link>
        <x-dropdown-link href="/">
            Three
        </x-dropdown-link>

    </x-dropdown>
</x-layout>
```

`dropdown.blade.php`:
```
<div
    x-data="{ open: false }"
    @click.away="open = false">
    <div @click="open = ! open">
        {{ $trigger }}
    </div>

    <div x-show="open">
        {{ $slot }}
    </div>
</div>
```

`dropdown-link.blade.php`:
```
<a {{ $attributes }}>
    {{ $slot }}
</a>
```
- `$attributes` contains the `href` attribute



## [Nav Link Component](https://laracasts.com/series/blade-component-cookbook/episodes/7)

`partials/header.blade.php`:
```
<header>
    <nav>
        <x-nav-link route="home">Home</x-nav-link>
        <x-nav-link route="about">About</x-nav-link>
        <x-nav-link route="testimonials">Testimonials</x-nav-link>
        <x-nav-link route="contact">Contact</x-nav-link>
    </nav>
</header>
```

`components/nav-link.blade.php`:
```
<a
    href="{{ route($route) }}"
    class="{{ Route::is($route) ? 'text-blue-500 underline' : '' }}">
    {{ $slot }}
</a>
```
Checking active state to change styling of link for the page the user is currently on:  
- use named routes
- `Route::is()` or `Request::routeIs()` can accept the route name to check if the current request is for that route and then place different styling on the link
   * in the `nav-link` example above, the `text-blue-500` and `underline` CSS classes will be added if the current route matches the link
