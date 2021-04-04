# controller
controller 位置: root_dir/app/Http/Controllers/

<br/>

### 1.建立 controller
```
$ php artisan make:controller <controller_name>
```

### 2.controller URL傳遞參數
```php
// homeController.php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class homeController extends Controller
{	
	// 用 function 建立
    function home(Request $request)
    {
		// $_GET 可以改成 $request(自定義的名稱，只要是 Request 類別就好)
        // $keys = $_GET['keys'];
		$keys = $request->input('keys');
		
		// 參數1 = 指定的模板
		// 參數2 = 變數 array
        return view('layouts/home',[
            'key1' => $keys,
        ]);
    }
}
```
### 3.web.php 建立路由
```php
// web.php

// 1. 先 use 進來
use App\Http\Controllers\homeController;


// 2. 設定路由
// 參數2 的 array，第二個放  function 名稱
Route::get('/home', [homeController::class, 'home']);
```
### 4. template 放變數
```html
<!-- home.blade.php -->
<p>
	{{ $key1 }}
</p>
```

<br/>

# Resource Controller
多加入 --resource 指令，會多建立 7個路由，包括 CRUD 等功能
```
$ php artisan make:controller <Controller_name> --resource
```
設定 route 
```php
// web.php
use App\Http\Controllers\PhotoController;

// Route::resource(name, class)
// name 會直接設定 URI
Route::resource('photos', PhotoController::class);
```
### 產生以下 7 個路由
|Verb|URI|Action|Route Name|
|--|--|--|--|
|GET|/photos|index|photos.index|
|GET|/photos/create|create|photos.create|
|POST|/photos|store|photos.store|
|GET|/photos/{photo}|show|photos.show|
|GET|/photos/{photo}/edit|edit|photos.edit|
|PUT/PATCH|/photos/{photo}|update|photos.update|
|DELETE|/photos/{photo}|destroy|photos.destroy|

<hr/>

<br/>

### 通常不會將每個功能都開啟，需要再把功能開啟，所以需要設定功能是否開啟

```php
// web.php
// 只開啟 index, show 兩個 function
Route::resource('photos', PhotoController::class)->only(['index', 'show']);  

// 只關閉 index, show 兩個 function
Route::resource('photos', PhotoController::class)->except(['index', 'show']);
```
### 檢查所有 route 路徑
```
$ php artisan route:list
```
### Route Name 的用法
通常 blade 模板中，網址會使用 Route Name，方便維護
```html
<!-- index page (名稱見上方表格) -->
<a href="{{ route('products.index'}}">
    <img src="{{ $product['imageUrl'] }}" width=400 alt="">
</a>


<!-- show page (需要有參數 photo，此例為 product) -->
<a href="{{ route('products.show', ['product' => $product['id']]) }}">
    <img src="{{ $product['imageUrl'] }}" width=400 alt="">
</a>
```
```php
// productController.php

namespace App\Http\Controllers;
use Illuminate\Http\Request;

class productsController extends Controller
{
    private function getProducts()
    {
        return [
            [
                "id" => 1,
                "imageUrl" => asset("images/iphone.jpg")
            ],
            [
                "id" => 2,
                "imageUrl" => asset("images/macbook.jpg")
            ],
        ];
    }

    // index.blade 給 二維陣列
    // product.blade 給 一維陣列


    public function index()
    {   
        $products = $this->getProducts();
        return view('layouts/index', [
            'products' => $products
        ]);
    }

    
    public function show($id)
    {   
        // 轉 $id 型別 string >> int
        $id = intval($id);
        $index = $id - 1;
        $products = $this->getProducts();
        
        // 例外就給 404
        if ($index <= 0 && $index > count($products))
        {
            abort(404);
        }
        $product = $products[$index];
        return view('layouts/product', ['product' => $product]);
    }
}
```
```php
// 只需要寫一行

Route::resource('products', productsController::class);
```
			