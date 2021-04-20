# SQL 資料庫

## Laravel 中選擇要使用的 SQL
在 /config/database.php 中，可看到 default 指向 .env，要更改資料庫種類需要到 .env 改。
```php
// database.php
// 讀取 .env 檔案的 DB_CONNECTION，若為空，則預設是 mysql
'default' => env('DB_CONNECTION', 'mysql'),
```

### 使用 mysql 的環境配置
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

<hr/>

### 使用 sqlite 的環境配置
sqlite 沒有像 mysql、pgsql、sqlsrv 有 server 程式，有不占資源 (RAM) 的優點。缺點為速度較慢 (因檔案存放在硬碟)。
```php 
// .env
// 路徑使用正斜線

DB_CONNECTION=mysql
DB_DATABASE="C:/Users/Liang/Desktop/laravel_test/database.sqlite"
DB_FOREIGN_KEYS=true    // sqlite 需手動開啟
```
使用 <code>pwd</code> 指令，複製 DB_DATABASE 路徑，並在根目錄建立 <code>database.sqlite</code>。

最後必須在 <code>php.ini</code> 檔案中加入 extension，否則  <code>migrate</code> 時會報錯。
```php
// php.ini 配置檔
extension=php_pdo_sqlite.dll
```


<br/>

<br/>

## 建立 model 
model 位置在 <code>App\Http\model\\<model_name></code>
```php
// model 名稱需為單數
$ php artisan make:model Post
```

<br/>

## 產生 Migrations
### 手動，自行定義 migration 檔名
首先建立 model
```php
$ php artisan make:model Post
```
再進行 migration
```php
// create_posts_table 為檔名
$ php artisan make:migration create_posts_table
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