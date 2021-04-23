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
// name 會直接設定 URI，記得加 s
Route::resource('photos', PhotoController::class);
```
### 產生以下 7 個路由
|Verb|URI|Action|Route Name|
|--|--|--|--|
|GET|/photos|index|photos.index|
|GET|/photos/{photo}|show|photos.show|
|GET|/photos/create|create|photos.create|
|POST|/photos|store|photos.store|
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

<br/>

<br/>

# Resource Controller 寫法

```php
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
```


## 1. index
* 變數給二維 array 再去 template 中用迴圈拉出資料
```php
public function index()
{
    $products = $this->getProducts();
    return view('layouts/index', [
        'products' => $products
    ]);
}
```

## 2. show 
* 變數給一維 array
```php
public function show()
{
    settype($id, 'integer');
    $index = $id - 1;
    $products = $this->getProducts();

    // $index 超過範圍就給404
    if ($index < 0 || $index >= count($products)){
        abort(404);
    }

    $product = $products[$index];
    return view('layouts/product', ['product' => $product]);
}
```
## 3. create
* 不用傳遞變數
* create 用 GET，store 用 POST
* action 給 route('products.store') 需加上 @csrf
```php
// productsController.php
return view('layouts/create');
```
```html
<!-- create.blade.php -->

<form method="POST" action="{{ route('products.store') }}">
    @csrf
    <input type="text" name="title">
    <button type="submit">Submit</button>
</form>
```

## 4.edit
* edit 用 GET，update 用 PATCH/PUT
* 在 blade 中，action 給 route('products.update', 參數)。
* 在 blade 中，form method 還是用 POST，但是需加上 @method('PATCH')，並不是直接改 form method。
```php
public function edit($id)
{   
    settype($id, 'integer');
    $index = $id - 1;
    $products = $this->getProducts();

    if ($index < 0 || $index >= count($products)){
        abort(404);
    }
    $product = $products[$index];
    return view('layouts/edit', ['product' => $product]);
}
```
```html
<!-- edit.blade.php -->

<form method="POST" action="{{ route('products.update', ['product' => $product['id']]) }}">
    @csrf
    <!-- 使用 PATCH 或 PUT 都可以 -->
    @method('PATCH')
    <input type="text" name="title">
    <button type="submit">Submit</button>
</form>
```
@method('PATCH')
```html
<!-- 兩者等價，其實可以自己打 -->

@method('PATCH')

<input type="hidden" name="_method" value="PATCH">
```


## 5. store
* create 會 action 給 store
* edit 頁面使用 PATCH/PUT 傳遞到 route(products.store)
* 通常會將 form 儲存給 SQL

### 方法一
```php
public function store(Request $request)
{   
    // 1. 先新增物件
    $product = new Product;

    // 2. 將屬性填入，使用 $request 實例和 input 方法
    $product->name = $request->input('name');
    $product->founded = $request->input('founded');
    $product->description = $request->input('description');

    // 3. save model
    $product->save();

    // 4.回 index page
    return redirect()->route('products.index');
}
```
input() 方法
```php
// 不需要擔心發出請求時使用的 HTTP 動詞，取得輸入資料的方式都是相同的
// POST 或 GET 都可以用以下方式抓取資料
$request->input();

// input() 方法 可以填入第二個參數作為 default
$request->input('name', 'Sally');
```
### 方法二 - 使用 array
```php
public function store(Request $request)
{
    $product = Product::create([
        'name' => $request->input('name'),
        'founded' => $request->input('founded'),
        'description' => $request->input('description')
    ]);
    
    return redirect()->route('products.index');
}
```


<br/>

<br/>

## 6. update
* edit 會 action 給 update
* 可以使用 $request->method() 方法來檢查
* 通常會將 form 更新給 SQL
```php
public function update(Request $request, $id)
{
    echo "update!";
    $method = $request->method();
    echo $method;
}
```
## 7. destroy
* 將資料刪除，Route name 為 products.destroy 非 products.delete
```php
public function destroy($id)
{
    // code
}
```
```html
<!-- index.blade.php -->

@foreach($products as $product)
<form  method="POST" action="{{ route('products.destroy', ['product' => $product['id']]) }}">
    @csrf
    <!-- 方法用 DELETE -->
    @method('DELETE')
    <button type="submit">Delete</button>
</form>
@endforeach
```

<br/>

<br/>

# 重新導向 redirect
### 使用 redirect 函數
```php
public function update($id)
{   
    settype($id, 'integer');
    $index = $id - 1;
    $products = $this->getProducts();

    if ($index < 0 || $index >= count($products)){
        abort(404);
    }
    $product = $products[$index];

    // 導回首頁(無參數)
    return redirect()->route('product.index');

    // 導回 edit 頁面(有參數)
    return redirect()->route('product.edit', ['product' => $product['id']]);
}
```

