## Daily Practice
### Monday, December 21st, 2020
---


# [Browser Testing With Laravel Dusk](https://laracasts.com/series/browser-testing-with-laravel-dusk)


## [Post Tests](https://laracasts.com/series/browser-testing-with-laravel-dusk/episodes/2)

`->loginAs()`  
- accepts a `User` instance and logs in as that user
```
$this->browse(function (Browser $browser) {
    $browser->loginAs(User::find(1))
            ->visit('/home')
            ->assertSee('Home');
});
```

`tests/Browser/PostTest.php`  
```
<?php

namespace Tests\Browser;

use Illuminate\Foundation\Testing\DatabaseMigrations;
use Laravel\Dusk\Browser;
use Tests\DuskTestCase;

use App\Models\Post;
use App\Models\User;

class PostTest extends DuskTestCase
{
    use DatabaseMigrations;

    /** @test */
    public function a_user_can_view_index_of_posts()
    {
        $post = Post::factory([
            'user_id' => User::factory()->create()->id,
        ])->create();

        $this->browse(function (Browser $browser) use ($post) {
            $browser->visit('/')
                ->assertSee($post->title)
                ->assertSee('by ' . $post->user->name);
        });
    }

    /** @test */
    public function a_user_can_view_a_single_post()
    {
        $post = Post::factory([
            'user_id' => User::factory()->create()->id,
        ])->create();

        $this->browse(function (Browser $browser) use ($post) {
            $browser->visit(route('post.show', $post))
                ->assertSee($post->title)
                ->assertSee('By: ' . $post->user->name)
                ->assertSee($post->content);
        });
    }

    /** @test */
    public function a_user_can_create_a_post()
    {
        $post = Post::factory([
            'user_id' => User::factory()->create()->id,
        ])->create();

        $this->browse(function (Browser $browser) use ($post) {
            $browser->loginAs($post->user)
                ->visit('/')
                ->assertSee('Create Post')
                ->clickLink('Create Post')
                ->type('title', 'My First Post')
                ->type('content', 'My First Post Content')
                ->click('button[type="submit"]')
                ->assertSee('Post was successfully created')
                ->assertSee('My First Post')
                ->assertPathIs('/')
        });
    }

    /** @test */
    public function a_user_can_edit_a_post()
    {
        $post = Post::factory([
            'user_id' => User::factory()->create()->id,
        ])->create();

        $this->browse(function (Browser $browser) use ($post) {
            $browser->loginAs($post->user)
                ->visit('/')
                ->clickLink($post->title)
                ->assertSee('Edit Post')
                ->clickLink('Edit Post')
                ->type('title', 'My First Post edit')
                ->type('content', 'My First Post Content edit')
                ->click('button[type="submit"]')
                ->assertSee('Post was updated successfully')
                ->assertSee('My First Post edit');
        });
    }

    /** @test */
    public function a_user_can_delete_a_post()
    {
        $post = Post::factory([
            'user_id' => User::factory()->create()->id,
        ])->create();

        $this->browse(function (Browser $browser) use ($post) {
            $browser->loginAs($post->user)
                ->visit('/')
                ->clickLink($post->title)
                ->assertSee('Delete Post')
                ->click('button[type="submit"]')
                ->assertSee('Post was deleted successfully')
                ->assertDontSee($post->title);
        });
    }
}
```

[Dusk Selectors](https://laravel.com/docs/8.x/dusk#dusk-selectors)  
- add a `dusk` attribute to an HTML element
- prefix the selector with `@` to manipulate the attached element within a Dusk test
```
// HTML...
<button dusk="login-button">Login</button>

// Test...
$browser->click('@login-button');
```
