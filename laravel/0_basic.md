# 建立專案
使用 composer 安裝
```
$ composer create-project --prefer-dist laravel/laravel <project_name> "8.*"
```
或是使用 installer 安裝
```
$ laravel new <project_name>
```

<br/>

# 本地端開啟 Application
預設的 port 號為 8000
```
$ php artisan serve
```
也可以指定 port 號，如果有其他程序佔用同一個 port 號為
```
$ php artisan serve --port=8081
```
<br/>

# 幾個好用的 VSCode 外掛
### 有關 Laravel:  
|No.|Extension Name|Function|
|--|--|--|
|1|Laravel artisan|可以輸入額外 artisan 指令，或快捷鍵|
|2|Laravel snippets|自動輸入 laravel 語法 (基本syntax)|
|3|Laravel Extra Intellisense|自動輸入 laravel 語法 (多半為變數、views、route name、參數)|
|4|Laravel Blade snippets|支援 Blade 模板 highlight|
|5|Laravel Blade Spacer|支援 Blade 模板 自動完成下引號、括號|
|6|Laravel goto view|支援點選 view 切換視窗|
|7|Live Sass Compiler|webpack.mix 自動跑 npm run watch|
|8|Live Server|安裝 Live Sass Compiler 後會自動安裝|

<br/>

### 與 UI 相關: 
|No.|Extension Name|Function|
|--|--|--|
|1|Bracket Pair Colorizer|括號自動上色|
|2|JavaScript (ES6) code snippets|JS ES6 syntax|
|3|VSCode great icon|棒棒的 Icon|

<br/>

<br/>

# Route 路由
基本語法
```php
Route::http_method($url, $callback);

// 舉例
Route::get('/', function () {
    return view('welcome');
});
```

寫在 routes/web.php 檔案中
```php
// routes/web.php

use use App\Http\Controllers\homeController;

Route::get(
    '/home/{id}', 
    [homeController::class, 'home']
);
```


<br/>


# blade 模板
blade templates 的位置:  root_dir/public/views/

<br/>

### extends 繼承
```php
// 位置為 views/layouts/app.blade.php
// 用 . 表示分層
@extends('layouts.app')
```

### include
```php
@include('layouts.nav')
```

如果要使用 php 官方提供另外一種方式
```php
// 原本的方式，也會過
<?php
	// code 
?>

// 另一種
@php
	// code
@endphp
```
<br/>

### 透過 URL 傳遞參數
URL 的傳遞參數以 ? 開頭，之後放 key=value，例如
```
localhost:8000/app?key=value
```
web.php
```php
// routes/web.php

route::get('/app', function(){
	// 透過 變數存取 $_GET 內容
	$var = $_GET['key'];

	// 參數1 = 指定的模板
	// 參數2 = 要傳遞到模板的 array
	// 但不建議用這種寫法，通常 array 會放在 controller 中
	return view('layouts/app', ['var' => $var]);
})
```
app.blade.php
```html
<p>
	傳遞的參數為: {{ $var }}
</p>
```

### 讀取靜態內容(css, js, img)
```html
// 可以直接在 blade 模板中抓取，但通常會寫在 controller 中
// asset 函數可以抓取 public 中的靜態檔案

<img src="{{ asset('images/image.jpg') }}">
```
在 Controller 中抓取
```php
// controller.php

class homeController extends Controller
{	
    function show()
    {
		$imageUrl = asset('images/image.jpg');

        return view('layouts/show',[
            'imageUrl' => $imageUrl,
        ]);
    }
}
```
多圖片可以使用二維 array
```php
// controller.php

class homeController extends Controller
{	
    function show()
    {
		$products = 
		[
			[
				"imageUrl" => asset("images/image1.jpg")
			],
			[
				"imageUrl" => asset("images/image2.jpg")
			]
		]
		
		$product = $products[0];

        return view('layouts/show',[
            'product' => $product,
        ]);
    }
}
```
```hmtl
// product.blade.php

<img src="{{ $product["imageUrl"] }}">
```

### 呈上，在 URL 中加入 ID 讀取圖片
* Route 中加入 url/{id} 可以建立 id
* 需要限制 url 格式 (使用 regex)，[參考](https://laravel.com/docs/8.x/routing#parameters-regular-expression-constraints)
* 需要限制 id 的範圍 (使用 if else)
```php
// web.php
use App\Http\Controllers\homeController;

Route::get(
	'products/{id}',
    [homeController::class, 'home']
)->where(['id' => '[0-9]+']);		// regex
```
```php
// homeController
class homeController extends Controller
{	
	// 多一個參數 $id
	function home(Request $request, $id)
    {
        // 產品 array；array 的 index 即為 Route 的 id 
        $products = [
            [
                "imageUrl" => asset("images/iphone.jpg")
            ],
            [
                "imageUrl" => asset("images/macbook.jpg")
            ],
        ];

		// $id 從U，會自動轉型
		$id = intval($id);
        $index = $id -1;
        
        // exception
        if ($index < 0 || $index >= count($products)){
            abort(404);
        }
        // else
        $product = $products[$index];
            return view("layouts/home", [
            "product" => $product
        ]);
    }
}
```

```html
// home.blae.php

<img src="{{ $product['imageUrl'] }}" width=400 alt="oops">
```


<br/>



