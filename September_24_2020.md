## Daily Practice
### Thursday, September 24th, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Fetch the Most Recent Reply's Username](https://laracasts.com/series/how-do-i/episodes/7)

Tip with Factories:  
When creating a related model inside of a factory definition, wrap it inside of a closure.

For example:
```
$factory->define(App\Post::class, function (Faker\Generator $faker) {
    return [
        'user_id' => function () {
            return factory(App\User:class)->create()->id;
        },
    ];
});
```
Rather than:
```
$factory->define(App\Post::class, function (Faker\Generator $faker) {
    return [
        'user_id' => factory(App\User:class)->create()->id,
    ];
});
```

Wrapping the related model in a closure will allow you to override the factory without necessarily creating a new model;  
`factory('App\Post')->create(['user_id' => 5]);` will create a new `User` record, and _then_ swap out the ID with `5` if a closure is not used in the factory definition. Using a closure will just immediately set the `user_id` field to `5`.  

A few ways of getting the user that made the most recent reply:  
1. `$post->comments()->latest()->first()->user;`
2. Move the query in #1 to a method on the `Post` class, such as `latestComment()`: `$post->latestComment()->user;`
3. `latestComment()` returns `$this->hasOne(Comment::class)->latest();`: `$post->latestComment->user;`
4. `latestComment()` returns:
    ```
    $this->hasOne(Comment:class)->latest('comments.created_at')
        ->join('users', 'users.id, '=', 'comments.user_id')
        ->select('title', 'body', 'users.name');
    ```
    ```
    $post->latestComment->name;
    ```



## [Help Me Understand When to Use Polymorphic Relations](https://laracasts.com/series/how-do-i/episodes/8)

**Tip on Many-to-Many relationships:**  
In the migration for the pivot table, place a `unique()` function to ensure that there are no duplicate records:
```
Schema::create('favorites', function (Blueprint $table) {
    $table->increments('id');
    $table->integer('user_id')->index();
    $table->integer('post_id')->index();

    // combination of user_id and post_id can not have duplicates
    $table->unique(['user_id', 'post_id']);
});
```

**Difference between `$user->favorites()` and `$user->favorites`:**  
Model relationships as a function: `$user->favorites()`  
- returns a builder to continue creating the query  

Model relationships as a property: `$user->favorites`  
- returns a collection

**Polymorphic Many-to-Many relationships:**  

Example use-case: a user needs to be able to "favorite" both posts and comments  

Need `Favorite` class and migration (this is the pivot table):
```
class Favorite extends Model {
    protected $fillable = ['user_id'];
}
```
```
Schema::create('favorites', function (Blueprint $table) {
    $table->increments('id');
    $table->integer('user_id')->index();

    // foreign-key of favorited record
    $table->integer('favoritable_id');

    // stringified type of favorited record (ex: 'App\Post')
    $table->integer('favoritable_type');

    $table->unique(['user_id', 'favoritable_id', 'favoritable_type']);
});
```

`Favoritable` trait:
```
trait Favoritable {

    // polymorphic many-to-many
    // provide the class name and the prefix being used (ex: favoritable_id, favoritable_type)
    public function favorites() {
        return $this->morphMany(Favorite::class, 'favoritable');
    }
}
```

Use trait on `Post` model:
```
class Post extends Model {
    use Favoritable;
}
```

Use trait on `Comment` model:
```
class Comment extends Model {
    use Favoritable;
}
```

Query for the first `Post`'s favorited `User`s: `App\Post::first()->favorites;`  
Query for the first `Comment`'s favorited `User`s: `App\Comment::first()->favorites;`  

Can use the `Favoritable` trait on any classes that should be "favoritable"
