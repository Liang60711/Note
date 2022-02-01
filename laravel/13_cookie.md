# Cookie
在controlller中取cookie，有兩種方式
```php
// controller

public function getCookie(Request $request)
{
    // way1
    $value = $request->cookie('cart');

    // way2
    $value = Cookie::get('cart');
}

```

<br/>

<br/>

Laravel在只會使用加密的Cookie，所以如果要讀取沒有加密過的cookie，需到`App\Http\Middleware\EncryptCookies.php`，將cookie的key填上
```php
// EncryptCookies.php

protected $except = [
    'cart'
]
```

<br/>

<br/>

在後端寫入 cookie 的方法
```php
// controller
use Illuminate\Support\Facades\Cookie;

public function CartController(Request $request)
{
    // 讀取
    $value = $request->cookie('cart');

    // 更新 (使用object)
    if (!is_null($value))
    {
        $cart = json_decode($cart, true);
        $cart[1] = $cart;
        $cart = json_encode($cart);
    }

    // way1
    // 不會回傳，直接設定
    // $minute 設定cookie過期時間
    Cookie::queue('cart', $cart, 1);

    // way2 
    // 回傳 cookie 並加上 view()
    // $minute 設定cookie過期時間
    return response()->view('cart.index')->cookie('cart', $cart);
}
```

<br/>

<br/>

若要自訂 cookie 可以使用
```php
// controller
use Illuminate\Support\Facades\Cookie;

function setCookie()
{
    $cart = Cookie::get('cart');

    // ver 7.x
    // 'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    Cookie::queue
    (
        Cookie::make('cart', $cart, 86400, null, null, false, false);
    )

    // ver 8.x 可直接用 cookie()
    cookie('cart', $cart, 86400, null, null, false, false);

    return response()->view('cart.index')->cookie('cart', $cart, 86400, null, null, false, false);
}
```