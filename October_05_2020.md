## Daily Practice
### Monday, October 5th, 2020
---


# [Blade Component Cookbook](https://laracasts.com/series/blade-component-cookbook)


## [Prerequisite - Blade Components Explained](https://laracasts.com/series/blade-component-cookbook/episodes/1)

New paradigm for extending blade views (similar to Vue component syntax):  
In `layout.blade.php` component: `{{ $slot }}`
- `$slot` is a placeholder for whatever you pass into the component

In a blade view that extends the `layout` component:
```
<x-layout>
    Hello World
</x-layout>
```
- "Hello World" will be passed into `$slot` of the `layout` component


## [Flash Component](https://laracasts.com/series/blade-component-cookbook/episodes/2)

Flash Message:
```
<x-layout>
    <section class="bg-green-400 p-4">
        <div class="flex justify-between">
            <p>
                You are now logged out!
            </p>
        </div>
    </section>
</x-layout>
```

Using a `flash.blade.php` component:
```
<x-layout>
    <x-flash type="success">
        You are now logged out!
    </x-flash>
</x-layout>
```

`flash.blade.php`:
```
<section class="{{ $type === 'success' ? 'bg-green-400' : 'bg-red-400' }} p-4">
    <div class="flex justify-between">
        <p>
            {{ $slot }
        </p>
    </div>
</section>
```
- check the `$type` prop to set the background color class

`@props` directive
- specify which attributes should be considered data variables
- can set defaults
```
@props([
    'type' => 'success',
    'colors' => [
        'success' => 'bg-green-400',
        'error'   => 'bg-red-400',
        'warning' => 'bg-orange-400'
    ]
])

<section class="{{ $colors[$type] }} p-4">
    <div class="flex justify-between">
        <p>
            {{ $slot }
        </p>
    </div>
</section>
```
- `$colors[$type]` will check the `$colors` array (in the `@props` directive) for a matching key based on the `$type` property that is passed to the component

Attributes Bag
- holds any attributes that are passed in but not declared as data attributes by `@prop`  

Example - adding an `mt-10` class:
```
<x-layout>
    <x-flash class="mt-10">
        Great job!
    </x-flash>
</x-layout>
```
```
@props([
    'type' => 'success',
    'colors' => [
        'success' => 'bg-green-400',
        'error'   => 'bg-red-400',
        'warning' => 'bg-orange-400'
    ]
])

<section {{ $attributes->merge(['class' => "$colors[$type] p-4" ]) }}>
    <div class="flex justify-between">
        <p>
            {{ $slot }
        </p>
    </div>
</section>
```
Will compile to:
```
<section class="bg-green-400 p-4 mt-10">
    <div class="flex justify-between">
        <p>
            {{ $slot }
        </p>
    </div>
</section>
```
- the `mt-10` class (from the `<x-flash>` declaration) was merged into the attributes bag



## [Form Component](https://laracasts.com/series/blade-component-cookbook/episodes/3)

`comments/edit.blade.php`:
```
<x-layout>
    <form method="POST" action="/comments/{{ $comment->id }}">
        @csrf
        @method('PATCH')

        // form inputs...

    </form>
</x-layout>
```

`comments/edit.blade.php` with a `form.blade.php` component:
```
<x-layout>
    <x-form
        method="PATCH"
        action="/comments/{{ $comment->id }}">

        // form inputs...

    </x-form>
</x-layout>
```

`form.blade.php` component:
```
@props([
    'method' => 'POST',
    'action' => ''
])

<form
    method="{{ $method === 'GET' ? 'GET' : 'POST' }}"
    action="{{ $action }}"
    {{ $attributes }}
>
    @csrf

    @if (! in_array($method, ['GET', 'POST']))
        @method($method)
    @endif

    {{ $slot }}

</form>
```
