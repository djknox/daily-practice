## Daily Practice
### Wednesday, October 14th, 2020
---


# [Multitenancy in Practice](https://laracasts.com/series/multitenancy-in-practice)


## [Use Stubs to Set the Tenant ID](https://laracasts.com/series/multitenancy-in-practice/episodes/3)

Create `Department` model with migration: `php artisan make:model Department -m`  
`Department` model migration:
```
public function up()
{
    Schema::create('tenants', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->unsignedBigInteger('tenant_id');
        $table->timestamps();
    });
}
```

Stubs
- Artisan console's `make` command uses stub files to generate classes
- can customize these stubs if needed

publish stubs (in order to customize) with: `php artisan stub:publish`
- stubs are published to `/app/stubs` directory

Customize `migration.create.stub` so that any time a migration is created, it will include the `tenant_id` field  
`migration.create.stub`:
```
public function up()
{
    Schema::create('{{ table }}', function (Blueprint $table) {
        $table->id();
        $table->unsignedBigInteger('tenant_id');
        $table->timestamps();
    });
}
```
- now, any time a migration is created it will come with the `tenant_id` field
- can also customize `factory.stub` to add the `tenant_id` to every model factory that is created with Artisan


Create a form with Livewire: `php artisan make:livewire department-form-component`
- this will put the component at:
   * `app/Http/Livewire/DepartmentForm.php`
   * `app/resources/views/livewire/department-form.blade.php`

`DepartmentForm.php`:
```
class DepartmentForm extends Component
{
    public $name;
    public $success = false;

    public function submit()
    {
        Department::create([
            'name' => $this->name,
        ]);

        $this->success = true;
    }

    public function mount($departmentId = null)
    {
        if ($departmentId) {
            $this->name = Department::findOrFail($departmentId)->name;
        }
    }

    public function render()
    {
        return view('livewire.department-form');
    }
}
```

`department-form.blade.php`:
```
<div>
    <input
        wire:model="name"
        type="text" />

    <button wire:click="submit">
        Submit
    </button>

    @if($success)
        <div>
            Saved
        </div>
    @endif
</div>
```
- the component can be used in other views with `<livewire:department-form />`

`Department.php`:
```
class Department extends Model
{
    protected $fillable = [
        'name',
    ];

    protected static function booted()
    {
        static::addGlobalScope(new TenantScope);

        if (session()->has('tenant_id)) {
            static::creating(function ($department) {
                $department->tenant_id = session()->get('tenant_id');
            });
        }
    }
}
```
- the `booted()` method will set the `tenant_id` on the `Department` instance while it is being created
- since the `booted()` method should also exist on the `User` model, it would be a good idea to customize the `model.stub` file so it is added any time a model is created with Artisan

Create `Phone` model with migration and factory: `php artisan make:model Phone -mf`  
- stubs will structure the model class, migration, and factory based on the customized stub files


__Takeaways__:
- stub files can be customized so that any time `php artisan make:...` is run, the class will be created with the customizations
- a `booted()` method can be added to Model classes to add Global Scopes or tap in to model events such as `creating` to make changes that should exist on every model instance
