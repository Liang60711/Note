# Laravel 操作 DB 的2種方式
### 1. Query builder
### 2. Eloquent ORM

<br/>

<br/>

# Query builder CRUD
為 fluent interface 風格的一種 api。 目的為簡化程式碼撰寫量並增加可讀性。
```php
// Non-fluent
DB::select(['table' => 'posts', 'where' => ['id' => 1]])

// fluent
DB::table('posts')->where('id', 1)->get();
```


## Select
```php
// controller.php

// 因為需要 DB 記得 use
use Illuminate\Support\Facades\DB;
```
get() 抓取 collection；first() 抓取 array
```php
// controller.php

// 選取 table 中所有資料
DB::table('posts')->get();

// 選取 table 中第一筆資料
DB::table('posts')->first();

// 選取 PrimaryKey = 5 的一筆資料；find 函式是抓取 PK
// 若只有一筆資料在 blade 中不能用 foreach
DB::table('posts')->find(5);

// 選取單欄 column
DB::table('posts')->select('title')->get();

// 選取多欄 column
DB::table('posts')->select(['title', 'id'])->get();
```
### count, max, min, sum, average
```php
// 資料筆數
DB::table('posts')->count();
DB::table('posts')->whereBeteen('votes' [1, 3])->count();

// 欄位抓取最大值
DB::table('posts')->max('votes');

// 欄位抓取最小值
DB::table('posts')->min('votes');

// 欄位內數值總數
DB::table('posts')->sum('votes');

// 欄位內數值平均 (float 4 位數)
DB::table('posts')->avg('votes');
```

### where
```php
// where 加上條件
DB::table('posts')->where('id', 1)->first();

// where + like 條件
DB::table('posts')->where('title', 'like', '%a')->first();

// whereBetween 指定範圍區間 (7,8,9 都會取到)
DB::table('posts')->whereBetween('id', [7, 9])->get();

// whereNotNull 選取欄位非 Null 的資料
DB::table('posts')->whereNotNull('title')->get();

// distinct 選取不重複的值 (若重複，只取第一筆)
DB::table('posts')->select('title')->distinct()->get();
```
### orderBy, groupBy
```php
// orderBy 排序 (asc, desc)
DB::table('posts')->orderBy('title', 'desc')->get();

// orderBy 多次排序
DB::table('posts')->orderBy('title', 'desc')->orderBy('body', 'asc')->get();

// 最早(latest) 或 最晚(oldest) 的資料 (依照 created_at 時間排序)
DB::table('posts')->latest()->first();

// 亂數排序
DB::table('posts')->inRandomOrder()->get();

// groupBy + having (having 類似 where)
DB::table('posts')->groupBy('id')->having('id', '>', 100)->get();

// groupBy 多次
DB::table('posts')->groupBy('id', 'title')->having('id', '>', 100)->get();
```
使用 dd() 函式進行輸出，會中斷程式，並在瀏覽器印出。
```php
// controller.php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;

class postsController extends Controller
{
    public function index ()
    {
        // dd() 會中斷函式，所以 $post 不會被印出
        $asd = "123";
        dd($asd);
        $post = DB::table('posts')->get();
        dd($post);
    }
}
```

<br/>

## Insert
```php
// 新增一筆資料，一維陣列
DB::table('posts')->insert(
    [
        'title' => 'apple',
        'body' => 'apple is good',
        'email' => '123@gmail.com'
    ]
);


// 新增多筆資料，二維陣列
$data =[
    ['title1' => 'apple', 'body1' => 'apple is good', 'email1' => '123@gmail.com'],
    ['title2' => 'apple', 'body2' => 'apple is good', 'email2' => '123@gmail.com']
]

DB::table('posts')->insert($data);
```
<br/>

## Update
```php
// update
DB::table('posts')->where('id', 1)->update(['status' => 1]);
```
<br/>

## Delete
```php
// 刪除 table
DB::table('posts')->delete();

// 條件刪除
DB::table('posts')->where('votes', '>', 100)->delete();
```

<br/>

<br/>

<br/>

# Eloquent ORM
## ORM (Object-Relational Mapping)
目的: 將關聯式資料庫的資料，映射到物件 (Object) 之中，延續了物件導向的寫法，簡單來說就是物件導向風格的資料庫寫法。 

## Eloquent 簡介
只要建立了 model 和 資料表，即可從資料庫中抓取資料。可以將 Eloquent model 想成加強版的 Query Builder，並且可以同樣為 fluent 風格。

<br/>

# Eloquent ORM CRUD
## Select
以 DB 這個 class 進行操作
```php
// 記得 use model
use App\Models\Flight;
```
all(), find(), get()
```php
// 取所有資料
Flight::all();

// 取 PK = 5 一筆資料 (若只有一筆資料在 blade 中不能用 foreach)
Flight::find(5);

// 取 PK 多筆資料
Flight::find([5, 6, 7]);

// 取條件值
Flight::where('name', 'FR 900')->get();
```
count(), sum(), avg
```php
// count
Flight::all()->count();
Flight::where('price', '>', 300)->count();

// sum
Flight::sum('price');

// avg；return 浮點數 4 位
Flight::avg('price');
```

where
```php
// where 欄位條件
Flight::where('price', '>', 300)->get();

// where 多欄位條件 (二維陣列)
$query = [['number', '=', 'FR 900'], ['price', '<', '300']]
Flight::where($query)->get();

// where('active', '=', 1)， = 可省略
Flight::where('active', 1)
                ->orderBy('name', 'desc')
                ->take(10)
                ->get();

// 從 0 開始，取 10 筆
// offset 從 0 開始算
Flight::offset(0)
    ->limit(10)
    ->get();
```
chunk 切分查詢
```php
// 第一個參數: 每次「切分」要取出的資料數量。
// 第二個參數: 閉合函數 (closure function) 會在每次取出資料時被呼叫。

$flights = Flight::chunk(2, function($flights) 
{
    foreach($flights as $flight)
    {
        //
    }
});
```
Not Found Exceptions
```php
// findOrFail；取 PK = 1，如果沒有就 return 404
$flights = Flight::findOrFail(1);

// firstOrFail + where；取 where 條件第一筆，如果沒有就 return 404
$flights = Flight::where('price', '>', 300)->firstOrFail(1);
```
cursor 方法建立 generator
```php
// cursor 會使用 generator，將 query 使用較少的 RAM
// 將 cursor 裝進 generator 並用 foreach 讀取

foreach (Flight::where('destination', 'Zurich')->cursor() as $flight) {
    //
}
```

<br/>

<br/>

## Insert (與 query builder 寫法與差異較大)
可見 <code>1_controller</code> 筆記
```php
// controller --resource

public function store(Request $request)
{   
    // 使用 create 就不用 save
    $car = Car::create([
            'name' => $request->input('name'),
            'founded' => $request->input('founded'),
            'description' => $request->input('description')
        ]);
    
    return redirect()->route('cars.index');
}
```

## Update
update 在 HTTP 方法中是 PUT/PATCH 所以模板中的 Form 記得要加上 <code>@method('PUT')</code>
```php
// controller --resource

public function update(Request $request, $id)
{   
    // 使用 where 或 find 都可以
    $car = Car::where('id', $id)
        ->update([
            'name' => $request->input('name'),
            'founded' => $request->input('founded'),
            'description' => $request->input('description')
        ]);
    
    return redirect()->route('cars.index');
}
```

<br/>

<br/>

## Delete
Delete 在 HTTP 方法中是 Delete 所以模板中的 Form 記得要加上 <code>@method('DELETE')</code>
```php
// controller --resource

public function destroy($id)
{
    $car = Car::find($id)->first();
    $car->delete();

    return redirect()->route('cars.index');
}
```
以上是 resource 預設的寫法，可以直接將 $id 改成該實例 model $car，直接刪除 model
```php
// controller --resource

public function destory(Car $car)
{
    $car->delete();
    return redirect()->route('cars.index');
}
```
呈上，因為在模板中就已經將 'car' 指向 $car->id 作為參數，所以可以直接有 $car 這個參數
```html
<!-- Delete --> 
<form method="POST" action="{{ route('cars.destroy', ['car' => $car->id]) }}">
    @csrf
    @method('DELETE')
    <button class="btn-xs btn-danger" type="submit">Delete</button>
</form>
```
### Soft Delete 軟刪除
會在指定的 model 中添加 <code>deleted_at</code>，將 model label，讀取暫時讀不到此 model。後續可以繼續操作將該筆資料**永久刪除**或**回復**。

```php
// Model file
use Illuminate\Database\Eloquent\SoftDeletes;

class Flight extends Model
{   
    // 使用實例
    use SoftDeletes;
}
```
使用 SoftDeletes 後，需要在 table 中新增 <code>deleted_at</code> 這個欄位
```php
Schema::table('flights', function ($table) {
    $table->softDeletes();
});
```
現在使用模型呼叫 delete 方法時， <code>deleted_at</code> 欄位會被更新成現在的時間戳。在查詢使用軟刪除功能的模型時，被「刪除」的模型資料不會出現在查詢結果裡。

<br/>

如果想查詢 被軟刪除的資料 使用 <code>withTrashed</code>
```php
$flight = Flight::withTrashed()->where('id', 1)->get();
```
如果想 恢復/永久刪除 軟刪除的資料 使用 <code>restore</code> 或 <code>forceDelete</code>
```php
// 恢復
$flight->restore();

// 使用 where 條件查詢並恢復
Flight::withTrashed()->where('id', 1)->restore();

// 永久刪除
$flight->forceDelete();
```

<br/>

<br/>

## Eloquent Serialization
### 序列化 Serialization
在做前端和後端的資料交換時，通常使用 JSON 作為格式，所以需要將 Eloquent 取出的 Collection/Model 格式轉換為 JSON 格式。

<br/>

### Eloquent 回傳的格式
* get() 回傳以 Model 為內容的 Collection 物件 <code>object(Illuminate\Database\Eloquent\Collection)</code>
* find() 或 first() 回傳 Model 物件 <code>object(App\Models\Car)</code>

### 轉換格式
```php
public function index()
{
    // PHP array 格式
    $cars = Car::all()->toArray();

    // Json 格式 (會使用字串包起來，所以無法呼叫 key)
    $cars = Car::all()->toJson();
    // Json decode 將上方的 Json字串 解開雙引號，格式為 object
    $cars = Json_decode($cars);

    return view('cars.index')->with('cars', $cars);
}
```
所以在 blade 中就不是用屬性讀取資料，而是用 Array
```html
<!-- 原本 -->
{{ $car->name }}

<!-- Array -->
{{ $car['name'] }}
```
### json_decode 函式
將 Json 字串轉為 PHP array 或 PHP object；預設是 false
```php
$json = '{"name":"John", "age":"31", "city":"New York"}';

// 轉為 array
$array = json_decode($json, true);
echo $array['name'];        // john

// 轉為 object 
$obj = json_decode($json, false);
echo $obj->name;            // 屬性呼叫 john
```
### json_encode 函式
將 PHP array 轉為 json字串
```php
$array1 = [
    'a' => '1',
    'b' => '2',
    'c' => '3'
];
$json = json_encode($array1);
var_dump($json);    // string(25) "{"a":"1","b":"2","c":"3"}"
```
<br/>

<br/>