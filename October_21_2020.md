## Daily Practice
### Wednesday, October 21st, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Object-Oriented Forms: Part 2](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/20)

Creating a `Form` class to represent the form and all its functionality:
```
class Form {
    constructor(data) {
        this.originalData = data;

        for (let field in data) {
            this[field] = data[field];
        }

        this.errors = new Errors();
    }

    data() {
        let data = {};

        for (let property in this.originalData) {
            data[property] = this[property];
        }

        return data;
    }

    reset() {
        for (let field in originalData) {
            this[field] = '';
        }

        this.errors.clear();
    }

    submit(requestType, url) {
        axios[requestType](url, this.data())
            .then(this.onSuccess.bind(this))
            .catch(this.onFail.bind(this));
    }

    onSuccess(response) {
        alert(response.data.message);

        this.reset();
    }

    onFail(error) {
        this.errors.record(error.response.data));
    }
}
```
The form class is now responsible for:
- setting the fields on the form
- handling the errors
- resetting the form fields and errors
- submitting the form

instantiating and submitting the form in the main Vue component:
```
data () {
    return {
        form: new Form({
            name: '',
            description: '',
        }),
    };
},

methods: {
    onSubmit() {
        this.form.submit('post', '/projects');
    }
},
```
- create a new `Form` object and pass it the desired form fields and values
- when the submit button is clicked, call `onSubmit()` and delegate to the `form`'s `submit()`

JavaScript tips:
- `bind()` will set the `this` keyword to the provided value
   * useful when `this` context changes and needs to be re-bound
- `Object.assign()` copies all properties from one or more source objects to a target object and returns the target object
   * example:
        ```
        const target = { a: 1, b: 2 };
        const source = { b: 4, c: 5 };

        const returnedTarget = Object.assign(target, source);

        // target and returnedTarget are now:
        // { a: 1, b: 4, c: 5 }
        ```

## [Object-Oriented Forms: Part 3](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/21)

Returning a `Promise` from the `Form` class' `submit()` method:
```
class Form {
    submit(requestType, url) {
        return new Promise((resolve, reject) => {
            axios[requestType](url, this.data())
                .then(response => {
                    this.onSuccess(response.data);

                    resolve(response.data);
                })
                .catch(error => {
                    this.onFail(error.response.data);

                    reject(error.response.data);
                })
        });
    }

    onSuccess(response) {
        alert(response.data.message);

        this.reset();
        this.errors.clear();
    }

    onFail(errors) {
        this.errors.record(errors);
    }
}
```
- in `submit()`, now returning a `Promise`:
   * submit the AJAX request
   * if successful, pass the response data to `onSuccess()` and `resolve()`
   * if failure, pass the error response data to `onFail()` and `reject()`
- because a `Promise` is being returned, the `form.submit()` call within the main component's `onSubmit()` can now chain `then()` and `catch()` methods like it can with the `axios` form submission:
    ```
    onSubmit() {
        this.form.submit('post', '/projects')
            .then(data => console.log(data))
            .catch(error => console.log(errors));
    }
    ```
