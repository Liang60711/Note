# 建立 http server
```javascript
// 需要 http 模組
var http = require("http");

// 使用 匿名函數
http.createServer(function (request, response){ 
    // 1. 建立標頭
    response.writeHead(200, {"content-Type": "text/plain"});

    // 2. 建立內容
    response.write("Hello world");

    // 3. 結束
    response.end();
}).listen(8888);        // 監聽 port 使用 8888

// 在 terminal 中輸出文字，表示 server 正在運作
console.log('server is running');
```