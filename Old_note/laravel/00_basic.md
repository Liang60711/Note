# 建立專案
使用 composer 安裝
```
$ composer create-project --prefer-dist laravel/laravel <project_name> "8.*"
```
或是使用 installer 安裝
```
$ laravel new <project_name>
```

<br/>

# 本地端開啟 Application
預設的 port 號為 8000
```
$ php artisan serve
```
也可以指定 port 號，如果有其他程序佔用同一個 port 號為
```
$ php artisan serve --port=8081
```
<br/>

# 幾個好用的 VSCode 外掛
### 有關 Laravel:  
|No.|Extension Name|Function|
|--|--|--|
|1|Laravel artisan|可以輸入額外 artisan 指令，或快捷鍵|
|2|Laravel snippets|自動輸入 laravel 語法 (基本syntax)|
|3|Laravel Extra Intellisense|自動輸入 laravel 語法 (多半為變數、views、route name、參數)|
|4|Laravel Blade snippets|支援 Blade 模板 highlight|
|5|Laravel Blade Spacer|支援 Blade 模板 自動完成下引號、括號|
|6|Laravel goto view|支援點選 view 切換視窗|
|7|Live Sass Compiler|webpack.mix 自動跑 npm run watch|
|8|Live Server|安裝 Live Sass Compiler 後會自動安裝|

<br/>

### 與 UI 相關: 
|No.|Extension Name|Function|
|--|--|--|
|1|Bracket Pair Colorizer|括號自動上色|
|2|JavaScript (ES6) code snippets|JS ES6 syntax|
|3|VSCode great icon|棒棒的 Icon|

<br/>

<br/>

# Xdebug 安裝 (windows)
1. windows可以在terminal輸入 `php -i`，貼到 [`Xdebug installation wizard`](https://xdebug.org/wizard)來看要下載哪個檔案。
    * windows 10
    * VScode 1.63.2
    * php 8.0.15
    * xdebug 3.1.3


2. 下載`php_debug.dll`單個檔案

3. 設定 `php.ini`
    
    ```ini
    ; 下載檔案路徑
    zend_extension = "C:\Program Files\php-8.0.15\ext\php_xdebug.dll"
    xdebug.mode=debug
    xdebug.start_with_request=yes
    ; v2預設是9000，v3要改成9003
    xdebug.remote_port=9003
    ```

4. 使用 `phpinfo()` 查看 Xdebug 是否有執行。

5. 在 vscode 下載外掛 `PHP Debug`，並新增 `launch.json` 檔案


<br/>

<br/>

# 好用的網站
1. [Packagist.org](https://packagist.org/): 一個可以下載 PHP 套件的網站

<br/>

<br/>

# 專案結構
### 目錄
* app: 包含 controller 和 model
* bootstrap: 啟動 larael，開發上不太會用到
* config: 設置系統參數
* database: migration 檔案、 factory 假資料
* node_modules: node packages
* public: 包含 index.php，為 requests 進入點，網站可讀取的 assets (images, js, css)
* resources: 存放前台資源，包含 raw assets、views、language files
* routes: 路由設定
* storage: 包含編譯後的 blade 模板、session、cache、log 日誌檔案
* test: 自動化測試檔案
* vender: laravel 的依賴模組

### 檔案
* .env: 系統參數
* .env.example: .env 會有隱私問題(如資料庫密碼)，使用此檔作為測試
* composer.json: composer 管理套件的檔案
* composer.lock: 確定版本的 json 檔案
* package.json: npm 管理套件的檔案
* phpunit.xml: 設定 php unit test 檔案
* server.php: 模擬 apache 或 nginx 的設定，可以快速建立一個測試環境
* webpack.mix.js: 打包 js 和 css 設定檔案

<br/>

<br/>

# Composer 檔案
### 版本號怎麼看
composer.json 是安裝清單， composer.lock 是實際安裝版本。所以通常 composer.lock 都會加到 git 裡作版本控制。
```javascript
// ^ 符號代表 >= 7.0.1 但小於 8.0.0
// 三位數字代表 patch: alpha: beta

"guzzlehttp/guzzle": "^7.0.1",
```

### composer 指令
* 依照 composer.json 安裝套件，並建立 composer.lock和 vender 目錄，一般是在新建立專案時的步驟，寫好 composer.json，並執行 composer install。

    ```php
    $ composer install
    ```
* 依照 composer.json 更新套件，並更新 composer.lock 和 vender 目錄。

    ```php
    $ composer update
    ```
* 安裝新套件指令，並更新 composer.json 和 composer.lock
    ```php
    $ composer require
    ```

### composer.json
* 檔案中有兩個 key: 分別是 require 和 require-dev，差異在於分開正式環境及開發環境所依頼的 Package 或條件。
* 執行以下可以重新載入 require 和 require-dev 中的 class。或是在 package 中建立一個新的 class 之後。

    ```php
    $ composer dump-autoload
    ```

<br/>

<br/>

# 補充
## 正斜線 與 倒斜線
### windows 系統
* <code> / </code> : **URL**
* <code> \ </code> : **檔案路徑**

### UNIX 系統
* <code> / </code> : **檔案路徑**、**URL**
* <code> \ </code> : 轉義
* <code> - </code> 或 <code> -- </code> : 參數

<br/>

<br/>

# Laravel 中的命名
|檔案/物件名稱|命名方式 (以 car 舉例)|
|--|--|
|model|Car|
|controller|CarController|
|factory|CarFactory|
|seeder|CarSeeder|
|table 資料表|cars (只有資料表是 snake_case)|
|migration 檔案名稱|XXXX_XX_XX_create_cars_table|
