# Ajax
1. `XMLHttpRequest (XHR)` 是 JavaScript 的 API，
頁面 能透過它操作 HTTP 請求，進行網路作業，
擷取資料的同時，不進行頁面重載 (page reload)
2. ajax != XHR，XHR 僅是最常見的應用模式之一。

<br/>

<br/>

## 使用原生 js 實作 ajax
Laravel 中，若要使用 ajax，需要在html頁面中產生 `csrf_token`，通常會放在 meta 中。
```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

```js
// js

var formData = new formData();
var csrfTokenMeta = document.querySelector('meta[name="csrf-token"]');
var csrfToken = csrfTokenMeta.content;
formData.append('key', 'value');
// 在 laravel 中，需加入 _method, _token
formData.append('_method', 'DELETE'); // 或其他 method
formData.append('_token', csrfToken); // laravel 可以使用 csrf_token() 函式產生 token

// get
var request = new XMLHttpRequest();
request.open('get', actionUrl);
request.send();

// post
var request = new XMLHttpRequest();
request.open('post', actionUrl);
request.onreadystatechange = function(){
    if(request.readyState === XMLHttpRequest.DONE && request.status ===200){
        window.location.reload();
    }
}
request.send(formData);
```

<br/>

<br/>

## 使用原生 JavaScript 舉例 - 抓取pathName =  test/{id} 的資料內容
在 blade 模板中使用 JavaScript
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    
    <!-- ajax 函式 -->
    <script type="text/javascript">
        function getData(pathName){
            
            // XMLHttpRequest 為 js 原生物件，專門連線 server
            var req = new XMLHttpRequest();
            
            // open 方法 (HTTP method, URL)
            req.open("get", "http://127.0.0.1:8000/test/" + pathName);
            
            // onload時連線已完成
            req.onload = function(){
                var content = document.getElementById('content');
                content.innerHTML = this.responseText;
            };
            // 送出連線
            req.send();
        }
    </script>
    <title>Document</title>
</head>

<!-- 網頁初始化時就載入 id=1 -->
<body onload="getData('1');">
<div>
    @foreach ($cars as $car)
        <span onclick="getData('{{ $car->id }}');">{{ $car->name }}</span>
    @endforeach
</div>
<hr/>
<!-- 輸出資料位置 -->
<div id="content"></div>
</body>
</html>
```
controller
```php
class testController extends Controller
{
    public function index (Request $request)
    {
        $cars = Car::all();
        return view('test', compact('cars'));
    }

    public function car($id)
    {   
        $car = Car::where('id', $id)->first();
        $carModels = CarModel::where("car_id", $id)->get();
        return view('carModel_one', [
            'car' => $car,
            'carModels' => $carModels
        ]);
    }
}
```
Router
```php
Route::get('/test', [testController::class, 'index']);
Route::get('/test/{id}', [testController::class, 'car']);
```