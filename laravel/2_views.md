# blade 模板
blade templates 的位置:  root_dir/public/views/

<br/>


### extends 繼承
```php
// 位置為 views/layouts/app.blade.php
// 用 . 表示分層
@extends('layouts.app')
```


### @yield
自定義(挖洞)一個區塊給子模板使用，如果子樣板未定義，就不會顯示任何內容。
```html
<!-- base.blade.php -->
<title> @yield('title')  </title>

<main>
    @yield('main')
</main>
```
子樣板需要繼承 base.blade.php
```html
<!-- inherit.blade.php -->
<!-- 1.繼承父模板 -->
@extends('base')


<!-- 2.使用@section填入 -->
<!-- title 不用 endsection -->
@section('title', 'good websites')  

@section('main')
    <!-- code -->
@endsection
```


### @include
將已寫好模板嵌入進此區塊
```php
@include('layouts.nav')
```

### blade 模板
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


