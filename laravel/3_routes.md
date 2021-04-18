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

### 使用 Resource Controller
```php
// web.php
use App\Http\Controllers\PhotoController;

// Route::resource(name, class)
// name 會直接設定 URI，記得加 s
Route::resource('photos', PhotoController::class);
```
<br/>