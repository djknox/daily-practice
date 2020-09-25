## Daily Practice
### Friday, September 25th, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Create Nested Comments](https://laracasts.com/series/how-do-i/episodes/9)

`Comment` should have an optional `parent_id` field to connect it to the comment it is replying to.  

In the comment form, check if the `parent_id` is set - if it is, then add a hidden field that sends the parent comment's ID in the request.  
Example:
```
@if (isset($parentId))
    <input name="parent_id type="hidden" value="{{ $parentId }}">
    </input>
@endif
```

Group comments based on the `parent_id`:  
- `$post->comments->groupBy('parent_id')`

Top-level/root comments are those without a `parent_id`:
```
// this grouping creates collections with parent_id as the key
$comments = $post->comments->groupBy('parent_id');

// top-level/root comments have '' as the key
$rootComments = $comments[''];
```

Add `replies()` relationship to `Comment` model:
```
public function replies() {
    return $this->hasMany(Comment::class, 'parent_id');
}
```

Note:  
Can't eager load multiple levels down, so `N+1` problem will exist if this is tried.  
- For example: `$post->comments[0]->replies[0]->replies`  

One level down is perfectly fine: `$post->comments[0]->replies`


**Tip:**  
Any model can define a `newCollection()` method that will override the default `Illuminate\Database\Eloquent\Collection` and return your own custom collection class.   
For example:  
```
// Comment.php

public function newCollection(array $models = []) {
    return new CommentCollection($models);
}
```
```
// CommentCollection.php

use Illuminate\Database\Eloquent\Collection;

class CommentCollection extends Collection
{
}
```
