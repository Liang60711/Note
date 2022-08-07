## connection 和 queue 的區別
1. `connection` 定義佇列存放位置的設定，一個 connection 可以有多個 queue，如果沒指定 queue 的話，就會將工作放入各個 connection 定義的 default 佇列。
`queue` 

2. `queue` 為佇列名稱，可以對佇列任務進行「分類」，讓多個佇列任務之間分配不同的執行優先順序和 Queue Worker 的執行數量，`onQueue()`會將 queue 名稱記錄在 jobs 資料表的 queue欄位。

在 `config/queue.php` 設定 `connections`
```php
'connections' => [

    // 同步式driver，開發時用，會阻塞，就是及時執行工作的意思
    'sync' => [
        'driver' => 'sync',
    ],

    'database' => [
        'driver' => 'database',
        'table' => 'jobs',
        'queue' => 'default',
        'retry_after' => 90,
        'after_commit' => false,
    ],

    'ccsocial' => [
        'driver' => 'database',
        'table' => 'jobs',
        'connection' => 'ccsocial',
        'queue' => 'default',
        'retry_after' => 90,
        'after_commit' => false,
    ],

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => env('REDIS_QUEUE', 'default'),
        'retry_after' => 90,
        'block_for' => null,
        'after_commit' => false,
    ],

],

// 定義執行時出現錯誤的工作，存到什麼地方等待確認
'failed' => [
    'driver' => env('QUEUE_FAILED_DRIVER', 'database-uuids'),
    'database' => env('DB_CONNECTION', 'mysql'),
    'table' => 'failed_jobs',
]
```

## connection driver 驅動
`database` 作為驅動，需建置一個資料表用來放置任務
```sh
# 產生一個名為 jobs 的 migration 檔案
php artisan queue:table

# 產生一個放失敗任務的 migration 檔案
php artisan queue:failed-table

php artisan migrate
```
## 將任務排進列隊中
```php
// 將任務排進列隊中，可選擇 conection 和 queue
ProcessPodcast::dispatch($podcast); // __construct 參數
    ->onConnection('database')
    ->onQueue();
```
## 延遲執行任務
```php
// 加進列隊後的10分鐘後才會開始執行
ProcessPodcast::dispatch($podcast);
    ->delay(now()->addMinutes(10));
```
## 本地端執行任務
```sh
php artisan queue:work database
```
