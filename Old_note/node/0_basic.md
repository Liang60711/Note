# node basic
## npm 常用指令
```javascript
npm init                    // 建立 package.json

npm install express         // 區域安裝

npm install express -g      // 全域安裝

npm update express          // 更新套件

npm uninstall express       // 解除安裝套件

npm uninstall express -g    // 解除安裝 global 套件

npm ls                      // 列出安裝套件

npm ls -g                   // 列出安裝 global 套件

npm search                  // 搜尋套件名稱
```

## JSON 和 Javascript object
結構類似，但還是有不一樣:
1. JSON就是一組字串。
2. 在使用<code> { } </code>建立 Javascript 物件時，屬性名稱是可以有引號也可以沒有的，但在JSON格式中，該屬性名稱一定要有引號。
3. 若物件中有 function，是無法透過JSON傳遞的。

```javascript
var person =  {
    "name": "Molecule Man",
    "age": 29,
};

// 將物件轉成 JSON
var json1 = JSON.stringify(person);

// 將 JSON 解析成物件
var obj1 = JSON.parse(json1);
```


<br/>

<br/>

## 在 localhost 建立 http server
```javascript
// 需要 http 模組
var http = require("http");

// 使用 匿名函數
http.createServer(function (request, response){ 
    // 1. 建立標頭
    response.writeHead(200, {"Content-Type": "text/plain"});

    // 2. 建立內容
    response.write("Hello world");

    // 3. 結束
    response.end();
}).listen(8888);        // 監聽 port 使用 8888

// 在 terminal 中輸出文字，表示 server 正在運作
console.log('server is running');
```
## 呈上，將剛寫的 exports 成 module
```javascript
// server.js

var http = require("http");
var url = require("url");   // 載入 url 模組

// 將所有包成一個函數
function start(){
    // 匿名函式可以拿出來
    function onRequest(request, response){
        // 可以解析 url 並抓出 pathname
        var pathName = url.parse(request.url).pathname;
        response.writeHead(200, {"Content-Type": "text/plain"});
        response.write("Hello world / use module" + pathName);      // 輸出
        response.end();
    }
    http.createServer(onRequest).listen(8888);
    console.log('server is running');
}

// 輸出成模組
exports.start = start;
```
新增 index.js 檔案
```javascript
// index.js
// 1.將剛建立的模組實例化
var server = require("./server");

// 2.使用 function
server.start();
```

## 呈上，加入 router
新增 router.js
```javascript
// router.js
function route(pathName){
    console.log("About to route a request for " + pathName);
}

// 匯出檔案
exports.route = route;
```
在 server.js 加入 route 參數
```javascript
// server.js

var http = require("http");
var url = require("url");

function start(route) {
    function onRequest(request, response){
        var pathName = url.parse(request.url).pathname;

        // 加入 route 函數，並使用 pathName 當作參數
        route(pathName);
        response.writeHead(200, {"Content-Type": "text/plain"});
        response.write("Hello world / use module " + pathName);
        response.end();
    }
    http.createServer(onRequest).listen(8888);
    console.log('server is running');
}

exports.start = start;
```
index.js
```javascript
// index.js
var server = require("./server");
var router = require("./router");       // require 進來

server.start(router.route);             // route function 作為函數丟進 start 函數
```