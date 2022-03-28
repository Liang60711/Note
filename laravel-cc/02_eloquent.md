## eloquent fresh, refresh
> 當流量較大或是程式跑比較久時，會發生取出的資料已經被改寫了，可以使用: 
1. `fresh` will get a new instance of the model from the database and return it.
    
    ```php
    $flight = Flight::where('number', 'FR 900')->first();
     /**
     * 中間程式正在跑時，此筆資料被刪除
     */ 
    $freshFlight = $flight->fresh(); // 更新至資料庫最新狀態
    ```

2. `refresh` will refresh the current model instance (including relationships). 
    ```php
    $flight = Flight::where('number', 'FR 900')->first();
 
    $flight->number = 'FR 456';
    
    $flight->refresh();
    
    $flight->number; // "FR 900"

    ```

<br/>

<br/>

## migrate fresh, refresh
```php 
php artisan migrate:fresh
/*
Dropped all tables successfully.
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table
*/

php artisan migrate:refresh
/*
Rolling back: 2014_10_12_100000_create_password_resets_table
Rolled back:  2014_10_12_100000_create_password_resets_table
Rolling back: 2014_10_12_000000_create_users_table
Rolled back:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table
*/
```

<br/>

<br/>

## 批量賦值
只有使用 `::create` 或 `::update` 才會用 `$fillable` 篩資料。
```php
class Product extends Model
{
    protected $fillable = ['name', 'price', 'imgUrl'];
}
```
```php
public function show($id)
{
    // $fillable 會擋
    $car = Car::create($data);

    // $fillable 不會擋
    $car = new Car();
    $car->name = 'name';
    $car->price = 'price';
    $car->imgUrl = 'imgUrl';
    $car->save();

    // $fillable 不會擋 (但不會加上created_at, updated_at)
    $car = Car::insert($data);
}
```