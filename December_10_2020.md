## Daily Practice
### Thursday, December 10th, 2020
---


# [Billing With Laravel Cashier](https://laracasts.com/series/billing-with-laravel-cashier)


# Section 1: Stripe

## [Installation and Usage](https://laracasts.com/series/billing-with-laravel-cashier/episodes/1)

[Laravel Docs - Laravel Cashier (Stripe)](https://laravel.com/docs/8.x/billing)  

`composer require laravel/cashier`
- install Cashier composer package

`php artisan migrate`
- creates database tables:
   * `customer` columns 
      - on `Billable` model (by default: `User`), new fields will be created:
         * `stripe_id`
         * `card_brand`
         * `card_last_four`
         * `trial_ends_at`
   * `subscriptions` table
   * `subscription_items` table

Add `Billable` trait to `Users` model
```
use Laravel\Cashier\Billable;

class User extends Authenticatable
{
    use Billable;
}
```
- Cashier assumes that the `Billable` model will be the `User` class
   * can change this by setting a new `CASHIER_MODEL` .env variable: `CASHIER_MODEL=App\Models\User`

Configure Stripe API keys in .env file:  
`.env`
```
STRIPE_KEY=your-stripe-key
STRIPE_SECRET=your-stripe-secret
```

__REWATCH THIS LESSON FOR HOW TO SET UP STRIPE ELEMENTS (UI, Forms, etc.)__  
(difficult to translate into notes)  
Copy + Paste HTML, JS, and CSS for credit card form using `Stripe.js` + Stripe Elements  
- [Stripe Elements](https://stripe.com/docs/stripe-js)  

When form is submitted and validated, a hidden `paymentMethod` field is sent to the server  
Example:  
```
[
    "_token" => "xcjveroocxksdg892394jkld"
    "plan" => "price_1a983490sdnGHsd"
    "paymentMethod" => "pm_1Hmasdfoxsdksdfetg"
]
```

Creating subscriptions  
```
$request->user()
    ->newSubscription('productName', $request->plan)
    ->create($request->paymentMethod);
```

[Laravel Spark](https://spark.laravel.com/)  
- first-party package for handling billing via Stripe
- for new apps - not designed to be integrated into existing applications
