# Laravel Mix
* laravel 前端打包工具為 webpack，但因為語法分別為 php 和 javascript，故需要有一個 API，名為 Laravel Mix
* 需先安裝 node

<br/>

## 安裝 Laravel Mix
內建的 package-json 檔案中已經將 mix 寫入，只需 install
```php
$ npm install
```
補充 npm install:  
1. python 社群是以 venv 套件管理 package，並且用 requirements.txt 來記錄名稱。
2. node 則是以 npm 管理套件，而 npm 的做法為將每個專案中都建立一個 node_module 目錄，裡面存放所有的 package。
3. 而 package.json 作用等同於 requirements.txt。
4. 故以下指令作用是相同的  

    ```javascript
    // node-npm
    $ npm init
    $ npm install 
    ```
    ```python
    # python-pip
    $ pip freeze > requirements.txt
    $ pip install -r requirements.txt
    ```

<br/>

## webpack.mix.js 中定義需要打包的檔案
```php
// webpack.mix.js
mix.js('resources/js/app.js', 'public/js')
    .postCss('resources/css/app.css', 'public/css');
```

## Running Mix
將 css 和 js 整合輸出至 public 目錄
```php
// 整合
$ npm run dev

// 整合並以壓縮輸出
$ npm run prod

// 整合並可以隨時同步模式
$ npm run watch
```

<br/>

## 安裝 SASS
1. 檢查 package.json 中是否有 sass 和 sass-loader
2. 如果沒有則需要安裝

    ```php
    $ npm install -g sass
    ```
3. 更改 webpack.mix.js 內容，副檔名 scss 可以向下相容 sass

    ```javascript
    mix.js('resources/js/app.js', 'public/js')
    .sass('resources/css/app.scss', 'public/css');
    ```
4. 將 resources/css/app.css 副檔名改成 scss

<br/>

## SASS 巢狀結構
可以將 css 寫成巢狀結構，可讀性較佳
```scss
.card{
    width: 400px;
    border: 1px solid black;

    .title{
        font-size: 24px;
        color: red;
    }
}
```

## 新增/更新 sass
1. 假如有 AAA.scss 和 BBB.scss 兩個檔案，使用 @import 

    ```scss
    // BBB.scss

    @import "./AAA.scss";
    ```
2. webpack 設定

    ```javascript
    // 不更改輸出檔案名稱
    mix.sass('resources/css/app.scss', 'public/css');

    // 自行定義輸出名稱
    mix.sass('resources/css/app.scss', 'public/css/main.css');
    ```
3. 在 blade 中導入

    ```php
    <link rel="stylesheet" href="/css/app.css">
    ```

<br/>

## Plain CSS
即原生的 CSS
```javascript
// webpack.mix.js
// 可以將多個檔案壓縮成一個，並命名為 all.css 
mix.styles(
    [
        'public/css/AAA.css',
        'public/css/BBB.css'
    ],
    'public/css/all.css');
```

<br/>

## 複製檔案 及 資料夾
laravel 在網站中的靜態資源只會抓取 public 目錄底下的資料，所以需要將 resources 中的靜態資源複製到 public 目錄中。
[文件連結](https://laravel.com/docs/7.x/mix#copying-files-and-directories)

### 複製檔案
```javascript
mix.copy('node_modules/foo/bar.css', 'public/css/bar.css');
```
### 複製目錄
```javascript
mix.copyDirectory('resources/img', 'public/img');
```

<br/>

## 網址 Url Processing
(只限 style sheet 中) Url 從 resources 目錄被複製到 public 中，會自動添加 ?hash，防止 cache 以為是同一個檔案，並即時更新。
```scss
// public/css/app.css
.example {
    background: url(/images/example.png?d41d8cd98f00b204e9800998ecf8427e);
}
```
如果正在開發的話，也可以至瀏覽器中關閉 Cache:  
* F12 >> Network >> Disable cache

如果是使用絕對路徑 或 根目錄的形式，則不會加入 hash
```php
url('/images/thing.png')
url('http://example.com/images/thing.png')
```

<br/>

## 更改 google fonts
### 有兩種方式:  
1. 使用 CDN，但如果使用第三方套件，不確定 CDN 的穩定性
2. 直接將檔案下載至 resources 再用 webpack.mix 整合到 public

### 下載 google fonts 檔案
1. [google fonts](https://fonts.google.com/)
2. 點選字體(注意是否支援中文) >> Select this style
3. 連結至 CDN 的 href 
4. 將所有 CSS code 複製進 resources/css/fonts/fonts.scss (檔名隨意)，並從 app.scss 中 import
5. 將 Url 中 woff2 檔案下載(連結即下載)
6. 將下載檔案放至 resources/css/fonts/ 中
7. webpack.mix 會自動偵測 woff2 檔案，並新建 fonts 目錄，打包進去
8. 引用時使用相對路徑即可
    
    ```html
    <link rel="stylesheet" href={{ url("/css/app.css") }}>
    ```

<br/>

## React 安裝
官方推薦使用 babel/preset-react，一款 javasript 整合包
```php
// --save-dev 是安裝在 devDependencies 中
$ npm install --save-dev @babel/preset-react
```
webpack.mix 設定
```javascript
mix.js('resources/js/app.jsx', 'public/js')
   .react();
```

<br/>

## 使用完整 URL (加入 Domain)
* 目的: 使用外部連結，並不是全部連結都是同一個 Domain
1. 至 config/app.php，輸入

    ```php
    // 第二個參數為 default (MIX_ASSET_URL 找不到就為 null)
    'mix_url' => env('MIX_ASSET_URL', null),
    ```

2. 至 .env 輸入

    ```
    MIX_ASSET_URL=http://localhost:8000
    ```

<br/>

## 加入 version() 函數
* 目的: 將網址加入版號，防止 Cache 不更新。
* 範圍: javascript 和 css 的路徑都可以使用。
1. webpack.mix 加入 version() 函數。
    
    ```javascript
    mix.js('resources/js/app.js', 'public/js')
    .version();
    ```

2. blade 模板中，使用 mix() 函數。

    ```php
    // css
    <link rel="stylesheet" href={{ mix("/css/app.css") }}>


    // js
    <script src="{{ mix('/js/app.js') }}"></script>
    ```