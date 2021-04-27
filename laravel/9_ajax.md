# Ajax

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