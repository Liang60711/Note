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

<br/>

<br/>

## upsert()
>`updateOrCreate` 效率很低，db會先去select，没有找到再去插入數據，找到了再去更新，故使用`upsert`。

```php
$data = [
    ['goods_id' => 1,'title' => 'ttt111'],
    ['goods_id' => 2,'title' => 'ttt222'],
    ['goods_id' => 3,'title' => 'ttt333']
]

// updateOrCreate
foreach($data as $item) {
Product::query()->updateOrCreate(
        ['goods_id' => $item['goods_id']], $item
    );
}

// upsert 
// arg1, 需要insert或update的$data
// arg2, 為條件，如果和這些欄位完全相同的資料，則將數據修改為 arg3 的欄位。
// arg3, 要修改的欄位
Flight::upsert([
    ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
    ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
], 
['departure', 'destination'], 
['price']);
```