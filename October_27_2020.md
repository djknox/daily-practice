## Daily Practice
### Tuesday, October 27th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [SPAs and the Backend](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/27)

Loading an index of statuses from the database:
- make `Status` model and migration: `php artisan make:model Status -m`


`web.php`
```
Route::get('/statuses', function () {
    return App\Status::with('user')->latest()->get();
});
```

`Home.vue`
```
<template>
    // display statuses here using v-for
</template>

<script>
    import moment from 'moment';

    export default {
        data () {
            return {
                statuses: [],
            }
        },


        created () {
            axios.get('/statuses')
                .then(response =>
                    this.statuses = response.data
                );
        },

        methods: {
            postedOn (status) {
                return moment(status.created_at).fromNow();
            }
        },
    }
</script>
```
- `created()` is executed when the component initializes
- `postedOn()` uses `moment.js` to display human-readable dates
   * `npm install moment`


Alternatively, could wrap the API call in a `Status` class method  
`/models/Status.js`
```
class Status {
    static all(then) {
        return axios.get('/statuses')
            .then(({data}) => then(data));
    }
}

export default Status;
```

Using the `Status` model in `Home.vue`
```
<template>
    // display statuses here using v-for...
</template>

<script>
    import moment from 'moment';
    import Status from '../models/Status';

    export default {
        data () {
            return {
                statuses: [],
            }
        },


        created () {
            Status.all(({statuses}) => this.statuses = statuses);
        },

        methods: {
            postedOn (status) {
                return moment(status.created_at).fromNow();
            }
        },
    }
</script>
```
- breaking out the `Status`-specific API calls into a `Status` model is useful if there are many other API calls that should be defined


ES2015 tip:  
- possible to use object destructuring directly within function arguments  
Example:
    ```
    axios.get('/statuses')
        .then(response => this.statuses = response.data);
    ```
    can also be written:
    ```
    axios.get('/statuses')
        .then(({data}) => this.statuses = data);
    ```


Eloquent tip:
- models can have a `$hidden` array that specifies attributes that should not be returned in queries  
Example:
    ```
    protected $hidden = [
        'password', 'remember_token',
    ];
    ```
- can also limit fields returned from relationships with `->select()`  
Example:
    ```
    // Status.php model class

    public function (user)
    {
        return $this->belongsTo(User::class)
            ->select(['id', 'name']);
    }
    ```
    - will only return the `id` and `name` attributes when the `Status`-`User` relationship is queried
    - remember to always add `id` to `select()` so that the relationship is properly loaded



## [Vue Filters](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/28)

Refactoring the `postedOn()` method (from the previous lesson) using a filter:  
`Home.vue` from previous lesson
```
<template>
    <div v-for="status in statuses">
        // display other status information...

        <p>
            {{ postedOn(status) }}
        </p>
    </div>
</template>

<script>
    import moment from 'moment';
    import Status from '../models/Status';

    export default {
        data () {
            return {
                statuses: [],
            }
        },


        created () {
            Status.all(({statuses}) => this.statuses = statuses);
        },

        methods: {
            postedOn (status) {
                return moment(status.created_at).fromNow();
            }
        },
    }
</script>
```

`Home.vue` using a Vue filter
```
<template>
    <div v-for="status in statuses">
        // display other status information...

        <p>
            {{ status.created_at | ago }}
        </p>
    </div>
</template>

<script>
    import moment from 'moment';
    import Status from '../models/Status';

    export default {
        data () {
            return {
                statuses: [],
            }
        },

        filters: {
            ago (date) {
                return moment(date).fromNow();
            }
        },

        created () {
            Status.all(({statuses}) => this.statuses = statuses);
        },
    }
</script>
```
- `postedOn()` method is replaced by the `ago()` filter
