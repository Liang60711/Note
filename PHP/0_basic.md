## php 安裝(windows)
1. 下載 zip 檔案
2. 解壓縮至目標資料夾
3. 設定全域變數 C:\Program Files\php-7.4.16

<hr>

## Hello world
1. php 檔案

```php
<?php
    echo "Hello world";
?>
```

2. 執行指令
* -S建立server
* 本地端位置 端口8000
* 檔案路徑如有空白，則用字串
```
$ php -S 127.0.0.1:8000 C:\Users\Liang\Desktop\php-test\index.php
$ php -S 127.0.0.1:8000 "C:\Users\Liang\Desktop\php test\index.php"		//有字串
```

<hr>

## php.ini 檔案
1. 輸入 會看到 configutation file 是空的
```
$ php -i
```

2. 進入 php 目錄複製"php.ini-development"，並將新檔案命名 "php.ini"

3. 再次輸入檢查 ini 配置檔案
```
$ php -i
```
4. 在 php.ini 加入 extension
```h
// windows
extension=php_fileinfo.dll
extension=php_pdo_mysql.dll

// macOS, linux
extension=php_fileinfo.so
extension=php_pdo_mysql.so
```

<hr>


## 建立專案-使用 laravel installer
1. 安裝 laravel installer
* 必須先安裝 composer ，和修改 php.ini 檔案  

    ```
    $ composer global require laravel/installer
    ```


2. 建立專案資料夾

    ```
    $ laravel new <project_name>
    ```

3. 進入專案，需要執行以下，為專案產生一組key以供部分加密使用，可以在 .env 檔案中看到 APP_KEY

    ```
    $ cd <project_name>
    $ php artisan key:generate --ansi
    ```

4. 建立本地端 server
    ```
    $ php artisan serve
    ```

5. 查看 laravel 版本，不會是最新版本，但會是stable版本
    ```
    $ php artisan -V
    $ php artisan --version
    ```

<hr>

## 建立專案-用composer
1. 使用 composer 建立專案
    ```
    // * 為指定最大版本號
    $ composer create-project --prefer-dist laravel/laravel:^7.* <project_name>

    // 也可以直接指定版本號
        $ composer create-project --prefer-dist laravel/laravel:^7.0 <project_name>
    ```