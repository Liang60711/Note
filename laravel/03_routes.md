# Route 路由
## 基本語法
```php
Route::<http_method>($url, $callback);

// 使用 view 函數
Route::get('/', function () {
    return view('welcome');
});

// 使用 Array
Route::get('/users', function (){
    return ['PHP', 'Laravel', 'Html'];
});

// 使用 JSON object 輸出為 json 字串
Route::get('/users', function (){
    return response()->json([
        'name' => 'Apple',
        'color' => 'White'
    ]);
});

// redirect 回首頁
Route::get('/users', function (){
    return redirect('/');
});
```
## 使用 Controller
```php
// web.php
use App\Http\Controllers\productController;

// 方法1 - 新
Route::get('/products', [productController::class, 'show']);

// 方法2
Route::get('products', 'App\Http\Controllers\productController@show')
```


## 使用 URL 傳遞參數篩選
```php
// web.php

use use App\Http\Controllers\productController;

// 參數為 integer
Route::get('/products/{id}', [productController::class, 'show'])->where('id', '[\d]+');    // 使用 regex 限制 Url，若在條件之外則給 404

// 參數為 string
Route::get('/products/{name}', [productController::class, 'show'])->where('name', '[a-zA-Z]+');

// 參數有 integer 和 string
Route::get('/products/{name}/{id}', [productController::class, 'show'])->where([
    'name' => '[a-z]+',
    'id' => '[0-9]+'
]);

```
controller 中需要加入參數
```php
// controller.php
public function show($id) {
        $data = [
            1 => 'iphone',
            2 => 'samsung'
        ];
        
        return view('products.show', [
            'product' => $data[$id] ?? 'product ' . $name . ' doesn\'t exist.'
        ]);
}
```
使用 ?? 符號，若 ?? 前面的變數為 Null 時，返回 ?? 後面的值，可疊加使用。
```php
// $a 未定義
echo $a ?? 1 ?? 2;      // 1

// $a $b 未定義
echo $a ?? $b ?? 3;     // 3
```
<br/>

## Route Name
可將 route 命名，達到好管理的目的；而命名的變數為**全域變數**
```php
// route.php

// 使用 name 屬性命名
Route::get('/products', [productController::class, 'show'])->name('products.show');
```
可在 blade 和 controller 中使用 route() 函數呼叫
```php
// blade.php
<a href="{{ route('products.show') }}">123</a>

// controller.php
public function newPage()
{
    return redirect()->route('products.show');
}
```

## URL() 函式
可被用於應用程式的任何一個 URL。顯示完整 URL (包含 protocol 和 domain)。
```php
$post = App\Post::find(1);

echo url("/posts/{$post->id}");

// 輸出 http://example.com/posts/1
```

<br/>

## Route 函式
可被用於產生 URL 到被命名的路由。也會顯示完整 URL (包含 protocol 和 domain)。
```php
// web.php

Route::get('/post/{post}', function () {
    //
})->name('post.show');
```
```php
// controller
echo route('post.show', ['post' => 1]);

// 輸出 http://example.com/post/1
```

<br/>

## 使用 Resource Controller
```php
// web.php
use App\Http\Controllers\PhotoController;

// Route::resource(name, class)
// name 會直接設定 URI，記得加 s
Route::resource('photos', PhotoController::class);
```
<br/>

<br/>

# RouteServiceProvider
1. 路徑在 `App\Providers\RouteServiceProvider.php`。
2. 可以在此設定 Url 中參數的限制。
3. 此限制是全域的，變數名稱若相同則會互相影響。

    ```php
    // RouteServiceProvider.php

    public function boot()
    {

        // 1.加入限制
        // 2.若route中只要參數名稱命名為 {id} ，都會被影響。
        Route::pattern('id', '[0-9]+');

    }
    ```
    多半還是會使用原本的方法來限制參數。

    ```php 
    // route/web.php

    Route::get('/show/{id}', [Show::class, 'index'])->where('id', '[0-9]+');
    ```

<br/>

<br/>

# Route::prefix
1. 在一般的網站應用中，一定會有數個頁面是屬於同性質甚至有著相似的 URL，透過 Route Groups 可以批次處理這些 routes。
2. `prefix` 可以將 Route 前加上前贅字。

    ```php
    // web.php

    // 新增 admin/users 和 admin/list 路徑
    Route::prefix('admin')->group(function(){
        Route::get('users', function () {
            //
        });

        Route::get('list', function () {
            //
        });
    })
    ```

3. 若要加上 route name，需再設定 name

    ```php
    // web.php 

    // prefix 記得加 "."
    Route::prefix('admin')->name('admin.')->group(function(){
        Route::get('users', function () {
            //
        })->name('users');

        Route::get('list', function () {
            //
        })->name('list');
    })

    ```

<br/>

<br/>

# Route::fallback
1. 若連線至未定義 Route 時，可以使用 fallback() 顯示 `404` 或是 `首頁`。

    ```php
    // web.php
    use Illuminate\Http\Request;

    // 可以將 $request 丟進來
    Route::fallback(function(Request $request){
        // 回首頁
        return redirect('/');

        // 導向特定頁
        return redirect()->route('user/index');
    })
    ```

    或是自定義一個頁面


        ```php
        // web.php
        use App\Http\Controllers\PageController;

        Route::fallback([PageController::class, 'index']);
        ```

2. `fallback()` 的自動導向預設是 `302`，若要改成 `301` 則需多加一個參數。

    ```php
    // web.php

    Route::fallback(function(){
        // 302 (通常用302即可)
        return redirect('/');

        // 301
        return redirect('/', 301);
    })
    ```