# blade 模板
blade templates 的位置:  root_dir/resources/views/

<br/>


### extends 繼承
```php
// 位置為 views/layouts/app.blade.php
// 用 . 表示分層
@extends('layouts.app')
```

### @yield 和 @section
* @yield 負責**顯示**區塊中的內容
* @section 負責**定義**區塊中的內容
* 如果只有使用 @section 定義，但未使用 @yield 顯示，則不會顯示定義內容

<br/>

### @yield
顯示一個子模板定義的區塊，如果子樣板未定義(未寫@section)，則會顯示空白。
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

### @section('content'), @show, @parent
* 與 @yield 用法類似，但可以多一個預設值
* 因為 @section 只有定義功能，沒有顯示功能，所以需要加上 @show
```php
// base.blade.php

@section('content')
	預設值
@show
```
這邊 @show 的意思為 @endsection 加上 @yield 顯示區塊
```php
// 與上方等價
@section('content')
    預設值
@endsection
@yield('content')
```
@parent 作用為，可以將原模板的預設值帶入子模板
```php
// inherit.blade.php

@section('extra_js')
    @parent
    // code
@endsection
```


<br/>

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

<br/>

### 傳遞 data 給 view 的三種方法
1. 使用 compact() 函式
2. 使用 with 
3. 使用 array

    ```php
    // ProductsController.php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class productsController extends Controller
    {
        public function index(){
            $title = 'Laravel 8';
            $description = 'php framework';
            
            // 以下三選一
            // 1.compact 函式(可使用多個變數)
            return view('products.index', compact('title', 'description'));

            // 2. with 只能傳遞一個變數
            return view('products.index')->with('title', $title);

            // 3. array
            $data = [
                'title' => $title,
                'description' => $description
            ]
            return view('products.index', $data);

            // 4. array 若使用 foreach
            $data = [
                'title' => $title,
                'description' => $description
            ]
            return view('test', ['data' => $data]);
            
        }
    }
    ```
2. 使用 array


<br/>

<br/>

### 透過 URL 傳遞參數給 view
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

<br/>

<br/>


### asset() 函數讀取靜態內容(css, js, img)
```php
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

### blade 模板 - 檢查 NULL 的函數
1. isset(): 檢查的變數存不存在。
2. empty(): 檢查的變數內的值是否為空 (null、各種型態的0、空字串)
3. is_null(): 檢查變數是否為 null。

<br/>

||gettype()|isset()|empty()|is_null()|
|--|--|--|--|--|
|$x is undefined|NULL|FALSE|TRUE|TRUE|
|$x = null;|NULL|FALSE|TRUE|TRUE|
|$x = 0;|integer|TRUE|TRUE|FALSE|
|$x = "0";|string|TRUE|**TRUE**|FALSE|
|$x = 1;|integer|TRUE|FALSE|FALSE|
|$x = "";|string|TRUE|TRUE|FALSE|
|$x = "PHP";|string|TRUE|FALSE|FALSE|

<br/>

<br/>

<br/>

### blade 中的 forelse
為 blade 特有，為 foreach 邏輯 + 如果變數為空
```php
// blade.php

@forelse($datas as $data)
// 循環時的內容

@empty
// 若 $datas 為空 (null、各種型態的0、空字串) 時

@endforelse
```

### blade 中的 while 迴圈
```php
// blade.php

{{ $i = 0 }}
@while ($i < 10)
    <h1> $i </h1>
    {{ $i++ }}
@endwhile
```