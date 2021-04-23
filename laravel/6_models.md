# SQL 資料庫

## Laravel 中選擇要使用的 SQL
在 /config/database.php 中，可看到 default 指向 .env，要更改資料庫種類需要到 .env 改。
```php
// database.php
// 讀取 .env 檔案的 DB_CONNECTION，若為空，則預設是 mysql
'default' => env('DB_CONNECTION', 'mysql'),
```

## 使用 mysql 的環境配置
```php
// .env

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```
設定好後，可以輸入指令進入 mysql
```php
$ mysql -u root -p
```
然後進入 mysql WorkBench 建立 DB_DATABASE，編碼選擇 <code> utf8mb4 </code>。


<br/>

<hr/>

<br/>

## 使用 sqlite 的環境配置
sqlite 沒有像 mysql、pgsql、sqlsrv 有 server 程式，有不占資源 (RAM) 的優點。缺點為速度較慢 (因檔案存放在硬碟)。
```php 
// .env
// 路徑使用正斜線

DB_CONNECTION=mysql
DB_DATABASE="C:/Users/Liang/Desktop/laravel_test/database.sqlite"
DB_FOREIGN_KEYS=true    // sqlite 需手動開啟
```
使用 <code>pwd</code> 指令，複製 DB_DATABASE 路徑，並在根目錄建立 <code>database.sqlite</code> 檔案 (只有 sqlite 需要)。

最後必須在 <code>php.ini</code> 檔案中加入 extension，否則  <code>migrate</code> 時會報錯。
```php
// php.ini 配置檔
extension=php_pdo_sqlite.dll
```

<br/>

<br/>

## Migration 與 Model
**Migration**: 只負責撰寫資料表的架構 (columns)，以及管理資料表的版本。

**Model**: 設定資料表的設定 (通常是用 override)，如 Table Names、PrimaryKeys 開啟/關閉、timestamps 開啟/關閉。

<br/>

<br/>


## 建立 model (即 Eloquent Model)
位置:  <code>\App\models\\</code>
```php
// model 名稱需為單數
$ php artisan make:model Post
```
model 可設定資料表內容
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Car extends Model
{
    use HasFactory;

    // 可以 override 資料表名稱
    protected $table = 'cars';

    // 更換 pk
    protected $primaryKey = 'cars_id';

    // 關閉自動增加功能
    public $incrementing = false;

    // timestamp 預設為 true；必須要有 created_at 和 updated_at 這兩個欄位，否則會無法使用
    public $timestamps = false;

    // 自定義 dateFormat
    protected $dateFormat = 'U';
}
```
<br/>

## Model 中的 Mass Assignment 設定
### Mass Assignment 批量賦值
是一種 create 新資料的保護機制，把屬性資料陣列傳入，這些屬性值會經由批量賦值存成模型資料。然而，若盲目地將使用者輸入存到模型，可能會造成嚴重的安全隱患。如果盲目的存入使用者輸入，使用者可以隨意的修改任何以及所有模型的屬性。基於這個理由，所有的 Eloquent 模型預設會防止批量賦值。
```php
// controller
// create 不會成功，因為需要在 model 中設定 fillable

public function store(Request $request)
{
    $car = Car::create([
        'name' => $request->input('name'),
        'founded' => $request->input('founded'),
        'description' => $request->input('description')
    ])
}
```

定義 <code>$fillable</code> 和 <code>$guarded</code> 屬性來設定批量賦值的資料表欄位
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Car extends Model
{
    use HasFactory;

    // 可以被批量賦值的欄位
    protected $fillable = ['name', 'founded', 'description'];

    // 拒絕被批量賦值的欄位
    protected $guarded = ['id', 'password'];

    // 阻擋所有欄位被批量賦值
    protected $guarded = ['*'];
}
```

<br/>

## 產生 Migrations
位置:  <code>\database\migrations\\</code>  

建立 model
```php
$ php artisan make:model Post
```
再進行 migration
```php
// create_posts_table 為檔名
// 會自動抓 "posts" 作為 table 名稱
$ php artisan make:migration create_posts_table
```
可以自行定義 table 的名稱
```php
// 使用 --create= 參數
$ php artisan make:migration create_posts_table --create=posts
```

<Br/>

## 建立 model + 產生 Migrations (比上述更快的方法)
建立 model 的同時，自動建立 migration 檔案
```php
$ php artisan make:model Post -m
```

<br/>

## Migration 檔案結構
class 中會有 up() 方法和 down() 方法
* up() 方法用於在資料庫內增加新的資料表表、欄位、或索引。
* down() 方法會反向執行 up 方法的操作。
```php
// 2021_04_20_041255_create_posts_table.php
// Blueprint 實例: 裡面有許多方法來建立資料表欄位

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    /*
     * Run the migrations.
     */
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            // 建立 table 的欄位
            $table->increments('id');
            $table->string('title');
            $table->mediumText('body');
            $table->timestamps();
        });
    }

    /*
     * Reverse the migrations.
     */
    public function down()
    {
        Schema::dropIfExists('posts');
    }
}
```

<br/>

<br/>

## 執行 Migration
執行應用程式中所有未完成的遷移。
```php
$ php artisan migrate
```
如果在你執行時出現 class not found 的錯誤，試著在執行 <code>composer dump-autoload</code> 指令後再次執行一次遷移指令。

<br/>

## 還原 Migration
還原遷移至上一版本的 migration。
```php
$ php artisan migrate:rollback
```
還原應用程式的所有遷移
```php
$ php artisan migrate:reset
```
還原資料庫的所有遷移，接著再執行 migrate 指令。
```php
$ php artisan migrate:refresh

// 等價於
$ php artisan reset && php artisan migrate
```
<br/>

## 查看 Migration 修改紀錄
```php
$ php artisan migrate:status
```
<br/>

<br/>

<br/>

# Factory
## 作用
指定一個 model 並用 faker library 產生資料，用以測試資料庫。

<br/>

## 建立 factory
建立後的 factory 位置在 <code> /database/factories/</code> 中，基本上 factory 會自動搜尋 model 名稱 (Post)
```php
$ php artisan make:factory PostFactory
```
如果搜尋不到或是想自定義，可使用參數 <code>--model</code>
```php
$ php artisan make:factory PostFactory --model=Post
```
<br/>

## 產生 假資料
需要先修改剛產生的 <code>PostFactory.php</code> 檔案
```php
// PostFactory.php

namespace Database\Factories;

use App\Models\Post;
use Illuminate\Database\Eloquent\Factories\Factory;
use League\CommonMark\Block\Element\Paragraph;
use Illuminate\Support\Str;     // 視情況加入

class PostFactory extends Factory
{   

    // 這邊視情況使用 完整路徑
    // protected $model = \App\Models\Post;
    protected $model = Post::class;
    
    // 開始定義 factory 要產生的 fields
    public function definition()
    {
        return [
            'title' => $this->faker->title,
            'body' => $this->faker->paragraph,
            'created_at' => now(),
        ];
    }
}
```
修改後，進入 tinker
```php
$ php artisan tinker
```
輸入以下，建立假資料，count() 選擇建立幾筆
```php
$ Post::factory()->count(2)->create();
```

<br/>

## 建立 model 時，順便建立 factory, seed, controller, migration 參數
```php
// Generate a model and a FlightFactory class...
$ php artisan make:model Flight --factory
$ php artisan make:model Flight -f

// Generate a model and a FlightSeeder class...
$ php artisan make:model Flight --seed
$ php artisan make:model Flight -s

// Generate a model and a FlightController class...
$ php artisan make:model Flight --controller
$ php artisan make:model Flight -c

// Generate a model and a migration, factory, seeder, and controller...
$ php artisan make:model Flight -mfsc
```

<br/>

<br/>





<br/>

## Tinker 
Tinker 為 REPL （Read–eval–print loop，交互式命令行介面）。當輸入一段程式碼以後，會馬上將結果輸出在 command 上。