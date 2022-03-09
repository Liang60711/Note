## filesystem config
在路徑 `config\filesystems.php` 做設定

```php
// filesystems.php

return [
    // driver
    'default' => env('FILESYSTEM_DRIVER', 'local'),

    // 儲存位置
    'disks' => [

        'local' => [
            'driver' => 'local',
            'root' => storage_path('app'),  // storage/app/ 底下
        ],

        'public' => [
            'driver' => 'local',
            'root' => storage_path('app/public'),   // storage/app/public 底下
            'url' => env('APP_URL').'/storage',
            'visibility' => 'public',
        ],

        's3' => [
            'driver' => 's3',
            'key' => env('AWS_ACCESS_KEY_ID'),
            'secret' => env('AWS_SECRET_ACCESS_KEY'),
            'region' => env('AWS_DEFAULT_REGION'),
            'bucket' => env('AWS_BUCKET'),
            'url' => env('AWS_URL'),
            'endpoint' => env('AWS_ENDPOINT'),
            'use_path_style_endpoint' => env('AWS_USE_PATH_STYLE_ENDPOINT', false),
        ],

    ],

    // 軟連結
    'links' => [
        public_path('storage') => storage_path('app/public'),
    ],

];
```

建立檔案
```php
use Illuminate\Support\Facades\Storage;

// 選擇儲存的 disk
// local指的是config裡面的路徑
Storage::disk('local')->put('test.txt', 'apple');
Storage::disk('public')->put('test.txt', 'apple');
```

儲存寫法1 用$request  
> store(PATH, NEW_NAME, DISK);

```php
public function store(Request $request)
{
    // 使用 $request 直接把檔案存入
    // 這邊 local 路徑指的是在 storage/app/local 下，沒有此路徑會建立新目錄
    $path = $request->file('image01')->store('local');
    // 儲存在 storage/app/ 下
    $path = $request->file('image01')->store('/');

    return redirect()->route('index');
}
```
windows 環境可能會報錯，需要在 `php.ini` 掛上 extension
```conf
# php.ini
extension = php_fileinfo.dll
```

儲存寫法2
```php
// controller
use Illuminate\Support\Facades\Storage;

public function store(Request $request)
{
    // 路徑 / 同樣代表 storage/app/
    $path = Storage::putFile('/', $request->file('image01'));

    return redirect()->route('index');
}
```

</br>

</br>

修改儲存的檔名
```php
public function store(Request $request)
{
    $path = $request->file('image01')->storeAs('/', 'image_new01.jpg');
    return redirect()->route('index');
}
```

抓取原上傳檔案的名稱
```php
public function store(Request $request)
{
    $name = $request->file('image01')->getClientOriginalName();
    $path = $request->file('image01')->storeAs('/', $name);
    return redirect()->route('index');
}
```


<br/>

<br/>

## 建立軟連結
`storage/` 是無法訪問的，需要在 `public/storage` 建立軟連結，指向伺服器端的 `storage`
```sh
php artisan storage:link
```