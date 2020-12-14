## Daily Practice
### Monday, December 14th, 2020
---


# [Billing With Laravel Cashier](https://laracasts.com/series/billing-with-laravel-cashier)


# Section 1: Stripe

## [Handle Incoming Webhooks](https://laracasts.com/series/billing-with-laravel-cashier/episodes/5)

[Laravel Docs - Cashier: Handling Stripe Webhooks](https://laravel.com/docs/8.x/billing#handling-stripe-webhooks)  


By default, Cashier's webhook controller responds to the `/stripe/webhook` URL path.  
Full list of all webhooks:  
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `customer.updated`
- `customer.deleted`
- `invoice.payment_action_required`

Cashier's webhook controller is at `laravel/cashier/src/Http/Controllers/WebhookController.php`  
- `extend` this to create your own webhook controller to handle additional webhook events  
    Example:
    ```
    <?php

    namespace App\Http\Controllers;

    use Laravel\Cashier\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
        * Handle invoice payment succeeded.
        *
        * @param  array  $payload
        * @return \Symfony\Component\HttpFoundation\Response
        */
        public function handleInvoicePaymentSucceeded($payload)
        {
            // Handle the incoming event...
        }
    }
    ```
- define a route to the controller to overwrite the default route registered by Cashier's service provider  
    `routes/web.php`
    ```
    use App\Http\Controllers\WebhookController;

    Route::post(
        '/stripe/webhook',
        [WebhookController::class, 'handleWebhook']
    );
    ```

Stripe webhooks need to bypass Laravel's CSRF protection, so their URIs should be whitelisted:  
`App\Http\Middleware\VerifyCsrfToken.php`  
```
protected $except = [
    'stripe/*',
];
```

Cashier automatically includes a middleware which validates that incoming Stripe webhook requests are valid - need to set a `STRIPE_WEBHOOK_SECRET` environment variable (the webhook `secret` can be found in the Stripe account dashboard)  
