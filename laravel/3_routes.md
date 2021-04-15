# Route 路由
### 基本語法
```php
Route::http_method($url, $callback);

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

use use App\Http\Controllers\homeController;

Route::get(
    '/home/{id}', 
    [homeController::class, 'home']
);

// 與使用 Controller 無異，但在 Controller function 中需要加入參數 $id
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