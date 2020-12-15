## Daily Practice
### Tuesday, December 15th, 2020
---


# [Billing With Laravel Cashier](https://laracasts.com/series/billing-with-laravel-cashier)


# Section 2: Paddle

## [Subscriptions](https://laracasts.com/series/billing-with-laravel-cashier/episodes/6)

[Laravel Docs: Cashier (Paddle)](https://laravel.com/docs/8.x/cashier-paddle)  
[Paddle](https://paddle.com/)  
- More expensive than Stripe  
- Taxes are automatically calculated based on user's location  
- More popular in Europe  



## [Single Charges and Receipts](https://laracasts.com/series/billing-with-laravel-cashier/episodes/7)

[Laravel Docs: Cashier (Paddle) - Single Charges](https://laravel.com/docs/8.x/cashier-paddle#single-charges)  

`charge()` method on a `Billable` model:  
```
use Illuminate\Http\Request;

Route::get('/store', function (Request $request) {
    return view('store', [
        'payLink' => $user->charge(12.99, 'Action Figure')
    ]);
});
```

[Laravel Docs: Cashier (Paddle) - Charging Products](https://laravel.com/docs/8.x/cashier-paddle#single-charges)  

`chargeProduct()` method on a `Billable` model:  
```
use Illuminate\Http\Request;

Route::get('/store', function (Request $request) {
    return view('store', [
        'payLink' => $request->user()->chargeProduct($productId = 123)
    ]);
});
```



# Section 3: Mollie

## [Overview and Usage](https://laracasts.com/series/billing-with-laravel-cashier/episodes/8)

[Mollie](https://www.mollie.com/en)  
- alternative to Stripe/Paddle
- made for businesses in Europe
