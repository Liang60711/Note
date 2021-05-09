# Validation 驗證

## 內建驗證
目的: 在 controller 中直接進行驗證邏輯，可以過，但會顯得有點亂(驗證邏輯和業務邏輯寫在一起)。

<br/>

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

<br/>

## 表單驗證 FormRequest
目的: 將驗證邏輯寫在 <code>App\Http\Requests</code> 裡面，不用與 controller 混在一起寫，增加可讀性。
1. FormRequest 是一個自訂的請求類別，裡面包含驗證的邏輯。
2. 要使用 FormRequest，可以在 controller 中利用型別提示傳入請求。進入的請求會在控制器方法被呼叫前進行驗證。

輸入指令建立 form request
```php
$ php artisan make:request CreateValidationRequest
```
建立的檔案會在 <code>App\Http\Requests\ </code> 
```php
class CreateValidationRequest extends FormRequest
{
    public function authorize()
    {
        // 如果回傳 false，會自動回傳一個 HTTP 回應，包含 403 狀態碼， 而 controller 方法(function store) 不會被執行。

        // 如果你打算在應用程式的其他部分處理授權邏輯，只要從 authorize 方法回傳 true
        return true;
    }


    public function rules()
    {
        return [
            'name' => 'required',
            'founded' => 'required|integer|min:0|max:2021',
            'description' => 'required'
        ];
    }

    // 自訂錯誤訊息，此方法必須回傳一個陣列
    public function messages()
    {
        return [
            'name.required' => '標題是必填的',
            'founded.required'  => '訊息是必填的',
        ];
    }
}
```
將驗證寫在型別中，會在資料邏輯處理前便完成驗證，不會將**驗證邏輯**與**function邏輯**搞在一起。
```php
// controller

use App\Http\Requests\CreateValidationRequest;

public function store (CreateValidationRequest $request)
{
    // 驗證只需一行
    $request->validate();

    $car = Car::create([
        'name' => $request->input('name'),
        'founded' => $request->input('founded'),
        'description' => $request->input('description')
    ]);

    return redirect()->route('cars.index');
}
```

<br/>

<br/>

## 自訂驗證
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

