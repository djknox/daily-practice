## Daily Practice
### Tuesday, October 6th, 2020
---


# [Blade Component Cookbook](https://laracasts.com/series/blade-component-cookbook)


## [Form Button Component](https://laracasts.com/series/blade-component-cookbook/episodes/4)

Button for deleting a comment:
```
<form
    method="POST"
    action="/comments/{{ $comment->id }}">
    @csrf
    @method('DELETE')

    <div>
        <button type="submit">
            Delete Comment
        </button>
    </div>
</form>
```

As a blade component - `form-button.blade.php`:
```
<x-form
    method="{{ $method }}"
    action="{{ $action }}">
    <div>
        <button type="submit" {{ $attributes }}>
            {{ $slot }}
        </button>
    </div>
</x-form>
```
- note that the `form-button.blade.php` component references another component, `form.blade.php`, from the previous episode ([Form Component](https://laracasts.com/series/blade-component-cookbook/episodes/3))

Using the `form-button` component:
```
<x-form-button
    method="DELETE"
    action="/comments/{{ $comment->id }}"
    class="bg-blue-400 text-white">
    Delete Comment
</x-form-button>
```
- `$method` = "DELETE"
- `$action` = "/comments/{{ $comment->id }}"
- `$attributes` = "bg-blue-400 text-white"
- `$slot` = "Delete Comment"
