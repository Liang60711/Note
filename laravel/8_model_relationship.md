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