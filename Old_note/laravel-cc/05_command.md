## command
查看所有 command
```sh
php artisan list
```
自定義 command
```sh
php artisan make:command SendEmails
```
signature 參數用法
```php
// 必填參數
protected $signature = 'send:email {user}';

// Optional argument
protected $signature = 'send:email {user?}';

// Optional argument with default value
protected $signature = 'send:email {user=foo}';

// 自訂 options，queue 不能傳入值，有打回傳 true，反之 false
// php artisan send:email rico --queue
protected $signature = 'send:email {user} {--queue}';

// 自訂 options 並設定初始值（可傳入值）
// php artisan send:email rico --queue="foo"
protected $signature = 'send:email {user} {--queue=default}';

// Options 簡寫
// php artisan send:email rico -Q="foo"
protected $signature = 'send:email {user} {--Q|queue}';

// 參數加上說明 (冒號)
protected $signature = 'send:email
                        {user : The ID of the user}
                        {--queue= : Whether the job should be queued}';
```

取輸入的參數
```php
public function handle()
{
    // 取單一參數
    $userId = $this->argument('user');

    // 取所有參數:array
    $arguments = $this->arguments();

    // 取 option 參數
    $queueName = $this->option('queue');
    
    // 取 option 參數:array
    $options = $this->options();
}
```
執行 command 時，詢問問題
```php
// true 為 default
if ($this->confirm('Do you wish to continue?'), true) {
    //
}
```
output 文字
```php
public function handle()
{ 
    $this->info('The command was successful!');
}
```

<br/>

<br/>

## 使用 Artisan 來執行 command
call command，執行並帶入參數
```php
use Illuminate\Support\Facades\Artisan;

// array 帶參數
Artisan::call('mail:send', [
    'user' => 'foo', '--queue' => 'default'
]);

// 直接輸入
Artisan::call('mail:send foo --queue=default');
```

<br/>

<br/>

## Queueing Artisan Commands
```php
Artisan::queue('mail:send', [
    'user' => 'foo', '--queue' => 'default'
])
    ->onConnection('redis')
    ->onQueue('commands');
```

