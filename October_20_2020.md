## Daily Practice
### Tuesday, October 20th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Object-Oriented Forms: Part 1](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/19)

Form Request Validation - handling errors from server validation in an `Errors` class:  
the form submission:
```
methods: {
    onSubmit() {
        axios.post('/projects', this.$data)
            .then(response => alert('Success'))
            .catch(errors => this.errors.record(error.response.data));
    }
}
```

the `Errors` class that the Vue component uses:
```
class Errors {
    constructor() {
        this.errors = {};
    }

    has(field) {
        return this.errors.hasOwnProperty(field);
    }

    any() {
        return Object.keys(this.errors).length > 0;
    }

    get(field) {
        if (this.errors[field]) {
           return this.errors[field][0];
        }
    }

    record(errors) {
        this.errors = errors;
    }

    clear(field) {
        delete this.errors[field];
    }
}
```


JavaScript tips:
- `delete` operator
   * removes a property from an object
        ```
        const Employee = {
            firstname: 'John',
            lastname: 'Doe'
        };

        delete Employee.firstname;
        ```
- `hasOwnProperty()` method
   * returns a `boolean` indicating whether the object has the specified property as its own property (as opposed to inheriting it)
        ```
        const Employee = {
            firstname: 'John',
            lastname: 'Doe'
        };

        // returns true
        Employee.hasOwnProperty('firstname');
        ```
