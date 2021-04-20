# SQL 資料庫

## Laravel 中選擇要使用的 SQL
在 /config/database.php 中選擇需要的 SQL 類型。
支援同時連結不同的 SQL。
```php
// database.php
// 預設 mysql
'default' => env('DB_CONNECTION', 'mysql'),
```

### 設定 database 的環境設置
在 .env 檔案中設定，可選擇需不需要密碼
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

<br/>

## 建立 model 
model 位置在 <code>App\Http\model\\<model_name></code>
```php
// model 名稱需為單數
$ php artisan make:model Post
```