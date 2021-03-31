# 建立專案
需要先安裝 composer
```
$ composer create-project --prefer-dist laravel/laravel <project_name> "8.*"
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

<br/>

# controller
controller 位置: root_dir/app/Http/Controllers/

<br/>

### 1.建立 controller
```
$ php artisan make:controller <controller_name>
```

### 2.controller 傳遞參數
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