## 建立觀察者模式
1. 可以先建立`ObserverServiceProvider`，或要寫在`appServiceProvider` 也可以。
    ```sh
    php artisan make:provider ObserverServiceProvider
    ```

2. 若建立新的`serviceProvider`，需要在`app\config`加上，載入此`serviceProvider`。

    ```php
    return [
        'providers' => [
            //...
            App\Providers\ObserverServiceProvider::class,
        ],

    ];
    ```

3. 建立`observer`
    ```sh
    php artisan make:observer orderObserver
    ```

4. 在 `ObserverServiceProvider` 中將建立的觀察者寫入。

    ```php
    use Illuminate\Support\ServiceProvider;
    use App\Models\Order;
    use App\Observers\orderObserver;

    class ObserverServiceProvider extends ServiceProvider
    {
        public function boot()
        {
            Order::observe(orderObserver::class);
        }
    }
    ```

5. 在 `orderObserver` 中，寫入觸發條件

    ```php
    namespace App\Observers;

    use App\Models\Order;

    class OrderObserver
    {
        public function created(Order $order)
        {
            dump('order created!');
        }

        
        public function updated(Order $order)
        {
            dump('order updated!');
        }

    }
    ```