# Route 路由
### 基本語法
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

// 使用 JSON object
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
### 使用 Controller
```php
// web.php
use App\Http\Controllers\productsController;

// 方法1
Route::get('/products', [productsController::class, 'show']);

// 方法2 
Route::get('products', 'App\Http\Controllers\productsController@show')
```


### 使用 URL 傳遞參數
```php
// web.php

use use App\Http\Controllers\productsController;

// 參數為 integer
Route::get('/products/{id}', [productsController::class, 'show'])->where('id', '[\d]+');    // 使用 regex 限制 Url，若在條件之外則給 404

// 參數為 string
Route::get('/products/{name}', [productsController::class, 'show'])->where('name', '[a-zA-Z]+');

// 參數有 integer 和 string
Route::get('/products/{name}/{id}', [productsController::class, 'show'])->where([
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

### Route Name
可將 route 命名，達到好管理的目的；而命名的變數為**全域變數**
```php
// route.php

// 使用 name 屬性命名
Route::get('/products', [productsController::class, 'show'])->name('show');
```
可在 blade 和 controller 中使用 route() 函數呼叫
```php
// blade.php
<a href="{{ route('test') }}">123</a>

// controller.php
public function newPage()
{
    return redirect()->route('products.index');
}
```

### URL() 函式
可被用於應用程式的任何一個 URL。顯示完整 URL (包含 protocol 和 domain)。
```php
$post = App\Post::find(1);

echo url("/posts/{$post->id}");

// 輸出 http://example.com/posts/1
```

<br/>

### Route 函式
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

### 使用 Resource Controller
```php
// web.php
use App\Http\Controllers\PhotoController;

// Route::resource(name, class)
// name 會直接設定 URI，記得加 s
Route::resource('photos', PhotoController::class);
```
<br/>