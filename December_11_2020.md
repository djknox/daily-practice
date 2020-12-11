## Daily Practice
### Friday, December 11th, 2020
---


# [Billing With Laravel Cashier](https://laracasts.com/series/billing-with-laravel-cashier)


# Section 1: Stripe

## [Check the Subscription Status](https://laracasts.com/series/billing-with-laravel-cashier/episodes/2)

[Laravel Docs - Cashier: Checking Subscription Status](https://laravel.com/docs/8.x/billing/#checking-subscription-status)  
```
if ($user->subscribed('default')) {
    //
}
```
- `subscribed()` method returns `true` if the customer is subscribed to the specified subscription  

Route middleware using `subscribed()`  
- `php artisan make:middleware PayingCustomer`
- `php artisan make:middleware RedirectIfPaid`

`PayingCustomer`: redirect to billing page if not a subscribed user  
`Http/Middleware/PayingCustomer.php`  
```
public function handle(Request $request, Closure $next)
{
        if ($request->user() && ! $request->user()->subscribed('default')) {
            // This user is not a paying customer...
            return redirect('subscribe');
        }

        return $next($request);
}
```

`Http/Kernel.php`  
```
protected $routeMiddleware = [
    ...
    'payingCustomer' => \App\Http\Middleware\PayingCustomer::class,
];
```

`routes/web.php`  
```
Route::middleware(['payingCustomer'])->get('/members', function () {
    return view('members');
})->name('members');
```

`RedirectIfPaid`: redirect to members page if already a subscribed user  
`Http/Middleware/RedirectIfPaid.php`  
```
public function handle(Request $request, Closure $next)
{
        if ($request->user() && $request->user()->subscribed('default')) {
            // This user is a paying customer...
            return redirect('members');
        }

        return $next($request);
}
```

`Http/Kernel.php`  
```
protected $routeMiddleware = [
    ...
    'nonPayingCustomer' => \App\Http\Middleware\RedirectIfPaid::class,
];
```

`routes/web.php`  
```
Route::middleware(['nonPayingCustomer'])->get('/members', function () {
    return view('members');
})->name('members');
```

Using `subscribed()` in Blade views:  
```
@unless (auth()->user()->subscribed('default'))
    // only show if the user is not subscribed to the "default" plan
@endunless
```



## [Single Charges](https://laracasts.com/series/billing-with-laravel-cashier/episodes/3)

[Laravel Docs - Cashier: Simple Charge](https://laravel.com/docs/8.x/billing/#simple-charge)  

`charge()` method  
- makes a one-time charge against a customer
- arguments:
   * amount (in cents)
   * payment id
   * array of options  

Example:  
```
$user->charge(100, $paymentMethod, [
    'custom_option' => $value,
]);
```



## [Generate Invoices](https://laracasts.com/series/billing-with-laravel-cashier/episodes/4)

[Laravel Docs - Cashier: Charge With Invoice](https://laravel.com/docs/8.x/billing/#charge-with-invoice)  

`invoiceFor()` method  
- makes a one-time charge and offers a PDF receipt to the customer  
- charged immediately against the user's default payment method  
- arguments:
   * product name
   * amount (in cents)
   * array of billing options for the invoice item
   * array of billing options for the invoice itself

Example:  
```
$user->invoiceFor('Stickers', 500, [
    'quantity' => 50,
], [
    'default_tax_rates' => ['tax-rate-id'],
]);
```

With `invoiceFor()`, will probably need to do the following first:  
- make the user (or other Billable model) instance a customer in Stripe with `createAsStripeCustomer()`
   * `auth()->user()->createAsStripeCustomer();`
- provide the user (or other Billable model) instance's default payment method to their corresponding Stripe customer by passing it to `updateDefaultPaymentMethod()`
   * `auth()->user()->updateDefaultPaymentMethod($request->paymentMethod);`


[Laravel Docs - Cashier: Invoices](https://laravel.com/docs/8.x/billing/#invoices)  

`invoices()` method  
- retrieves an array of a Billable model's invoices (returns a collection of `Laravel\Cashier\Invoice` instances)  

Example:
```
$invoices = $user->invoices();
```

`downloadInvoice()` method  
- automatically generate the proper HTTP response needed to download a PDF of a given invoice

Example:
```
Route::get('/user/invoice/{invoice}', function (Request $request, $invoiceId) {
    return $request->user()->downloadInvoice($invoiceId, [
        'vendor' => 'Your Company',
        'product' => 'Your Product',
    ]);
});
```
