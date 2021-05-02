# One to Many
## Foreign Key Constraints (外鍵限制)
### 舉例: cars 與 car_models 為一對多關係
```php
// migration file

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCarsTable extends Migration
{
    // up
    public function up()
    {   
        // cars table
        Schema::create('cars', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->integer('founded');
            $table->longText('description');
            $table->timestamps();
        });

        // car_models table
        schema::create('car_models', function (Blueprint $table){
            $table->increments('id');
            // unsignedInteger (無符號整數)，指 0 或正整數
            // 負整數為 signedInteger (有符號整數)
            $table->unsignedInteger('car_id');
            $table->string('model_name');
            $table->timestamps();
            // constraint
            $table->foreign('car_id')
                ->references('id')
                ->on('cars')
                ->onDelete('cascade');  // 也可更換成 ->onDelete('set null');
        });
    }

    // down
    public function down()
    {
        Schema::dropIfExists('cars');
    }
}
```
### 若報出 exception 可以下
```php
// 刪除所有 tables，並重新跑所有 migrations
$ php artisan migrate:fresh
```
### migration 寫完了換 Model，主要是在 blade 中可以互相呼叫
```php
// Car
class Car extends Model
{
    use HasFactory;

    // 表示一對多
    public function carModels()     // 使用複數 (1 輛 car 可以有多個 carModels)
    {
        return $this->hasMany(CarModel::class);
    }
}
```
```php
// CarModel
class CarModel extends Model
{
    use HasFactory;

    public function car()           // 使用單數 (多個 carModels 只能有 1 輛車)
    {   
        // 表示多對一
        return $this->belongsTo(Car::class);
    }
}
```
blade 中使用 <code>foreach</code> 逐個抓出
```php
@foreach (car->carModels as $model)     // $model 這裡為 php object，用屬性呼叫
    {{ $model->model_name }}
@endforeach
```

<br/>

<br/>

# Has One Through
* 1 car has many car_model
* 1 car_model has many engines
* 1 car_model has 1 car_production_date
```php
car
    -id
    -name

------------------

car_model
    -id
    -car_id
    -carModel_name

car_production_date
    -id
    -carModel_id
    -created_at

------------------

engines
    -id
    -carModel_id
    -engine_name
```
has one through 寫法為 car 可以呼叫 car_production_date (透過中介關係 car_model)
```php
class Car extends Model
{
    public function carProductionDate()
    {
        /** 
        *   參數1: 最終要建立關係的 model
        *   參數2: 中介關係 model
        *   參數3: 中介 model 的 foreign key
        *   參數4: 最終 model 的 foreign key
        */
        return $this->hasOneThrough(CarProductionDate::class, CarModel::class, 'car_id', 'model_id');
    }
}
```


<br/>

<br/>

# Has Many Through
關係: 
* 1 car has many car_model
* 1 car_model has many engines
```php
car
    -id
    -name

car_model
    -id
    -car_id
    -carModel_name

engines
    -id
    -carModel_id
    -engine_name
```
has many through 寫法為 car 可以呼叫 engines (透過中介關係 car_model)
```php
class Car extends Model
{
    public function engines()
    {   
        /** 
        *   參數1: 最終要建立關係的 model
        *   參數2: 中介關係 model
        *   參數3: 中介 model 的 foreign key
        *   參數4: 最終 model 的 foreign key
        */

        return $this->hasManyThrough(Engine::class, CarModel::class, 'car_id', 'carModel_id');
    }
}
```