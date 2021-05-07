# Validation 驗證

## 使用內建驗證
將 <code>$request</code>所接收到的變數進行驗證:
* 成功: 則程式碼繼續(如儲存)。
* 失敗: 則 throw validation Exception。
```php
// controller --resource

public function store(Request $request)
    {   

        // 使用 | 符號區隔
        $request->validate([
            'name' => 'required|unique:cars',
            'founded' => 'required|integer|min:0|max:2021',
            'description' => 'required'        
        ]);

        $car = Car::create([
            'name' => $request->input('name'),
            'founded' => $request->input('founded'),
            'description' => $request->input('description')
        ]);

        return redirect()->route('cars.index');
    }
```
在模板中顯示
```html
@if ($error->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

<br/>

## 使用自訂驗證
創建的檔案位置: <code> \App\Rules\ </code>
```php
$ php artisan make:rule Uppercase
```
Rules 檔案
```php
class Uppercase implements Rule
{

    public function __construct()
    {
        
    }

    // 將條件寫在這邊
    // $attribute, $value 分別是 驗證欄位、使用者輸入值
    public function passes($attribute, $value)
    {
        return strtoupper($value) === $value;
    }

    // Exception 給的訊息
    // :attribute 為 驗證欄位名稱
    public function message()
    {
        return 'The :attribute must be uppercase.';
    }
}
```
最後在 Controller 中使用自定義的 Rules
```php
// controller --resource 
use App\Rules\Uppercase;

public function store(Request $request)
    {   
        // 直接 new
        $request->validate([
            'name' => new Uppercase,
            'founded' => 'required|integer|min:0|max:2021',
            'description' => 'required'        
        ]);

        $car = Car::create([
            'name' => $request->input('name'),
            'founded' => $request->input('founded'),
            'description' => $request->input('description')
        ]);

        return redirect()->route('cars.index');
    }
```