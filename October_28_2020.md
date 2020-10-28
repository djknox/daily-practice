## Daily Practice
### Wednesday, October 28th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Dedicated Form Components](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/29)

New routes:
```
Route::get('/statuses', StatusController@index);

Route::post('/statuses', StatusController@store);
```

Create `StatusController` for the `Status` model:
```
php artisan make:controller StatusController -m Status
```

`StatusController.php`
```
public function index()
{
    return Status::with('user')->latest()->get();
    
}

public function store(Request $request)
{
    // validation
    $this->validate($request, ['body' => 'required']);

    // create the status
    User::find(1)
        ->statuses()
        ->create([
            $request->only(['body'])
        ]);

    // return the status with the user
    return $status->load('user');
    
}
```

`User.php`
```
public function statuses()
{
    return $this->hasMany(Status::class);
}
```

`Status.php`
```
protected $fillable = ['body'];

public function user()
{
    return $this->belongsTo(User::class)
        ->select(['id', 'name']);
}
```

`Home.vue` (from the previous lesson)
- adding a form component; `<add-to-stream>`
```
<template>
    // display stream of statuses
    <div v-for="status in statuses">
        // display status information...
    </div>

    // form component for adding a status
    <add-to-stream @completed="addStatus">
    </add-to-stream>
</template>

<script>
    import moment from 'moment';
    import Status from '../models/Status';
    import AddToStream from '../components/AddToStream.vue';

    export default {
        components: {
            AddToStream,
        },

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

        methods: {
            addStatus (status) {
                this.statuses.unshift(status);

                alert('Your status has been added to the stream.');

                window.scrollTo(0, 0);
            },
        }
    }
</script>
```

`AddToStream.vue`
```
<template>
    <div class="message">
        <div class="message-header">
            Push to the Stream...
        </div>

        <div class="message-body">
            <form
                @submit.prevent="onSubmit"
                @keydown="form.errors.clear()"
            >
                <p class="control">
                    <textarea
                        v-model="form.body"
                        class="textarea"
                        placeholder="I have something to say..."
                    >
                    </textarea>
                    <span
                        v-if="form.errors.has('body')"
                        v-text="form.errors.get('body')"
                        class="help is-danger"
                    >
                    </span>
                </p>

                <p class="control">
                    <button
                        :disabled="form.errors.any()"
                        class="button is-primary">
                        Submit
                    </button>
                </p>
            </form>
        </div>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                form: New Form({
                    body: '',
                }),
            }
        },

        methods: {
            onSubmit () {
                this.form.post('/statuses')
                    .then(
                        status => this.$emit('completed', status)
                    );
            }
        },
    }
</script>
```
- this component will use the `Errors.js` class from [Object-Oriented Forms: Part 1](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/19) and the `Form.js` class from [Object-Oriented Forms: Part 2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/20)
   * both `Errors` and `Form` are saved in a `utilities` directory and globally imported in `bootstrap.js`



## [Testing Vue: Part 1](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/30)

[AVA](https://github.com/avajs/ava)
- test runner for Node.js

Starting from scratch:
- initialize a new `package.json`
   * `npm init -y`
- install `ava`
   * `npm install ava --save-dev`
- install `vue`
   * `npm install vue --save`
- install `babel-register` + `babel-preset-es2015` to transpile ES2015
   * `npm install babel-register --save-dev`
   * `npm install babel-preset-es2015 --save-dev`
- install `browser-env` to mock a browser environment
   * `npm install browser-env --save-dev`

`package.json` changes:
- configuring `babel` to use the `babel-preset-es2015` preset
   * alternatively, this could also be done with a `.babelrc` file
- adding `babel-register` and a helper file, `setup-browser-env.js` as a dependency to `ava`  

`package.json`
```
{
    // rest of package.json...
    "babel" {
        "presets": ["es2015"],
    },
    "ava": {
        "require": [
            "babel-register",
            "./test/helpers/setup-browser-env.js",
        ],
    },

    // rest of package.json...
}
```

`setup-browser-env.js`
- this will mock a browser environment; mocking `document`, `window` objects, etc.
```
import browserEnv from 'browser-env';

browserEnv();
```


`src/Notification.js`
- component to be tested
```
export default {
    template: '<div>{{ notification }}</div>',

    props: [
        'message',
    ],

    data () {
        return {
            message: 'Hello World',
        };
    },

    computed: {
        notification () {
            return message.toUpperCase();
        },
    },
};
```

`test/notification.js`
- file containing the tests
```
import Vue from 'vue/dist/vue.js';
import test from 'ava';
import Notification from '../src/Notification';

// set up component to use in tests
let vm;
test.beforeEach(t => {
    // N will be the Notification component constructor
    let N = Vue.extend(Notification);

    // create a new Notification component
    // with 'Foobar' as the message prop
    vm = new N({
        propsData: {
            message: 'Foobar',
        }
    }).$mount();
});

// test the root element's textContent
test('that it renders a notification', t => {
    t.is(vm.$el.textContent, 'FOOBAR');
});

// test the "notification" computed property
test('that it computes the notification', t => {
    t.is(vm.notification, 'FOOBAR');
});
```

Running tests:  
`node_modules/.bin/ava`
