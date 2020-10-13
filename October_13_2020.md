## Daily Practice
### Tuesday, October 13th, 2020
---


# [Multitenancy in Practice](https://laracasts.com/series/multitenancy-in-practice)


## [Meet Teamsy](https://laracasts.com/series/multitenancy-in-practice/episodes/1)

Multiple tenants in one database.  
Every database query is scoped to the tenant.  



## [Tenant Global Scope](https://laracasts.com/series/multitenancy-in-practice/episodes/2)

Single database, multi-tenant application.
- data should be segmented by tenant in the database
- a user should only be able to see data that belongs to their tenant
- a user should only be able to create data in their tenant

__Note:__ This lesson uses the TALL stack
- [Tailwind CSS](https://tailwindcss.com/)
- [Alpine.js](https://github.com/alpinejs/alpine)
- [Laravel](https://laravel.com/)
- [Livewire](https://laravel-livewire.com/)

Laravel TALL Preset - [https://github.com/laravel-frontend-presets/tall](https://github.com/laravel-frontend-presets/tall)  

Create `Tenant` model, migration, and factory: `php artisan make:model Tenant -mf`  
`Tenant` model migration:
```
public function up()
{
    Schema::create('tenants', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->timestamps();
    });
}
```

Add `tenant_id` and `role` fields to `User` model migration:
```
public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();

        // other fields...

        $table->string('role')->nullable();
        $table->unsignedBigInteger('tenant_id')->nullable();
    });
}
```
- `tenant_id` can be `nullable` so that admin users can be created that are not tied to a specific tenant

Global Scope to scope all queries to the user's tenant
- example - `tenant_id` scoped by a `where` clause: `User::where('tenant_id', 1)->count();`
- using a global scope will make the `where` clause unnecessary, and will add it implicitly to all queries: `User::count();` 
```
class TenantScope implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        if (session()->has('tenant_id')) {
            $builder->where('tenant_id', session()->get('tenant_id'));
        }
    }
}
```
- this is grabbing the logged-in user's `tenant_id` from the session
- another approach would be to grab the `tenant_id` from the logged-in user (`auth()->user()->tenant_id`), but this can cause issues with logging in/out

Also need to add a `booted()` method to the `User` model class to append the Global Scope:
```
protected static function booted()
{
    static::addGlobalScope(new TenantScope);
}
```
- when the `User` class is queried, `TenantScope` will be appended
- if using the approach of simply grabbing the `tenant_id` from the currently logged-in user, the `User@booted()` method will need to add a check to only append the `TenantScope` when there is a logged in user with a `tenant_id`:
   ```
   protected static function booted()
    {
        if (auth()->user()->tenant_id) {
            static::addGlobalScope(new TenantScope);
        }
    }
   ```

Need to grab the logged-in user's `tenant_id` and put it in the session:
- listen for the `Login` event
- event listener puts the user's `tenant_id` in the session

Create an event listener to grab the logged-in user's `tenant_id`: `php artisan make:listener SetTenantIdInSession`
```
public function handle($event)
{
    session()->put('tenant_id', $event->user->tenant_id);
}
```

Register the listener in `EventServiceProvider`'s `$listen` array for the `Login` event:
```
protected $listen = [

    // other events/listeners...

    \Illuminate\Auth\Events\Login::class => [
        SetTenantIdInSession::class,
    ],
];
```

Summary:
- when a user logs in, the `Login` event is dispatched
- the `SetTenantIdInSession` event listener, which listens for the `Login` event, then puts the user's `tenant_id` in the session data
- the session data is checked for a `tenant_id` and if one exists, the `TenantScope` global scope is added to the `User` model
- result: all queries on the `User` model will be scoped to the `tenant_id` of the currently logged-in user
