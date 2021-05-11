# artisan 指令
## 1. 查看指令
```
php artisan list
```

## 2. help
可以看指令詳細說明
```
php artisan help <command>
```
例如
```
php artisan help make:controller
```
```
php artisan help migrate
```

## 3. 優化載入
命令將常用的 class 合併到文件中，減少 loading 檔案，增加執行效率。會建立<code>bootstrap/cache/services.php</code> 和 
<code>bootstrap/cache/packages.php</code>。
```
php artisan optimize
```
將以上兩個 optimize 檔案刪除
```
php artisan clear-compiled
```

## 4. config 佔存檔案
目錄裡所有配置合併到一個文件裡面，減少載入的文件。合併檔案路徑: <code>bootstrap/cache/config.php</code>
```
php artisan config:cache
```
刪除<code>bootstrap/cache/config.php</code>
```
php artisan config:clear
```
## 5. Route 佔存檔案
指令會建立<code>bootstrap/cache/routes.php</code>
```
php artisan route:cache
```
刪除<code>bootstrap/cache/routes.php</code>
```
php artisan route:clear
````
## 6. 刪除資料庫中過期的登入 token
```
php artisan auth:clear-resets
```

## 7. 建立/更新 APP_KEY
建立/更新 <code> .env</code> 中 APP_KEY
```
php artisan key:generator
```

## 8. 建立 session migration 檔案
```
php artisan session:table
```

## 9. 清除 view 引擎 cache
```
php artisan view:clear
```