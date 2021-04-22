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
```php
// 取所有資料
Flight::all();

// 取 PK = 5 一筆資料 (若只有一筆資料在 blade 中不能用 foreach)
Flight::find(5);

// 取 PK 多筆資料
Flight::find([5, 6, 7]);

// where 欄位條件
Flight::where('price', '>', 300)->get();

// where 多欄位條件 (二維陣列)
$query = [['number', '=', 'FR 900'], ['price', '<', '300']]
Flight::where($query)->get();

// where + count
Flight::where('price', '>', 300)->count();

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
切分查詢
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