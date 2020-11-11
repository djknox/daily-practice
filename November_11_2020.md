## Daily Practice
### Wednesday, November 11th, 2020
---


# [Learn Vue 2: Step By Step](https://laracasts.com/series/learn-vue-2-step-by-step)


## [Workshop - Contact Support Modal](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/39)

Scenario: click on a "Support" link and a full-screen overlay modal appears  

Package for Vue-based modals: [`vue-js-modal`](https://github.com/euvl/vue-js-modal)  

`SupportButton.vue`
```
<template>
    <div>
        <a
            @click="$modal.show('contact-support-modal')"
        >
            Support
        </a>

        <modal
            name="contact-support-modal"
            height="auto"
            width="100%"
            :pivotY="1"
            classes="bg-white rounded-none shadow-inner"
        >
            <div>
                modal contents (form for submitting something to support)
            </div>
        </modal>

    </div>
</template>

<script>
    export default {
        data () {
            return {
                message: {},
                submitted: false,
                errors: {},
            };
        },

        methods: {
            cancel () {
                this.$modal.hide('contact-support-modal');

                this.resetForm();
            },
            contactSupport () {
                this.submitted = true;

                axios.post('/contact', this.message)
                    .catch(errors => {
                        this.errors = errors.response.data.errors;
                    })
                    .then(() => {
                        this.$modal.hide('contact-support-modal');

                        this.resetForm();
                    })
            },
            resetForm () {
                this.message = {};
                this.submitted = false;
            }
        }
    };
</script>
```

Random JS tip:  
[`Sweet Alert`](https://sweetalert.js.org/) is an NPM package that can render better-looking alert modals than the standard JS `alert()` function



## [Reusability](https://laracasts.com/series/learn-vue-2-step-by-step/episodes/40)

Scenario: an accordion-style component that displays FAQs --> make a generic accordion-style component that can be reused to display many things
1. the accordian can be a single instance of a Question + Answer pair/Title + Body pair/etc.
   * pass in props for `title` and `body`
2. the accordion can be the full collection of all Questions + Answers/Title + Body/etc.
   * covered in next lesson
