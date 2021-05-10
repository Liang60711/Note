# Hello world
## 設定 router
只要 Route 路徑有改，就需重啟 Server
```javascript
var express = require('express');

// app 有各種 function，之後可以呼叫
var app = express();

// 設定 router
// get
app.get('/', (req, res)=>{
    res.send('Hello World');
})
// post
app.post('/', (req, res)=>{
    res.send('POST is done');
})
// 使用 regex，會由上往下找，所以網址都找不到會用這個
app.get('/*', (req, res)=>{
    res.send('can not find page');
})



// 監聽
var server = app.listen(8081, ()=>{
    var host = server.address().address;
    var port = server.address().port;
    console.log('host: %s port: %s', host, port);
})
```

## 設定靜態網頁
可以在專案目錄內建立 <code>public</code> 目錄，底下可以放靜態網頁
```javascript
app.use(express.static('public'));
```

## 抓取 get 資料並回傳
```javascript
var express = require('express');
var app = express();

app.use(express.static('public'));

// get(URL, callback)
app.get('/get_form', (req, res)=>{
    response = {
        // req.query 屬性
        first_name1: req.query.first_name,
        last_name1: req.query.last_name
    }
    res.end(JSON.stringify(response));
})
```
之後把 html 中的 form action 指向 <code>./get_form</code>

## 抓取 post 資料並回傳
```javascript
var express = require('express');

// 解析 post 需要的模組
var bodyParser = require('body-parser');
var urlencodedParser = bodyParser.urlencoded({extended:false});

var app = express();
app.use(express.static('public'));

// post(URL, Parser, callback)
app.post('/post_form',urlencodedParser, (req, res)=>{
    response= {
        // req.body 屬性
        first_name: req.body.first_name,
        last_name: req.body.last_name
    }
    res.end(JSON.stringify(response));
})
```
## 上傳檔案
### 單一檔案
```javascript
var express = require('express');
// 需要另行安裝 multer
var multer = require('multer');
var upload = multer({dest: './tmp/'})
var app = express();

app.use(express.static('public'));

// file upload
app.post('/file_upload', upload.single('file'), (req, res, next)=>{
    console.log('field name: ' + req.file.fieldname)
    console.log('file name: ' + req.file.filename)
    console.log('file path: ' + req.file.path)
    console.log('file type: ' + req.file.mimetype)
    res.end('upload:' + req.file.filename)
})
```
### 多檔案 

```javascript
// file load many
app.post('/file_upload', upload.array('file', 3), (req, res, next)=>{
    for(i=0; i<req.files.length; i++){
        console.log('file origin name: ' + req.files[i].originalname);
        console.log('file name: ' + req.files[i].filename);
    }
    res.end('upload:' + req.files.length + 'files')
})
```
input 加上 multiple 屬性，可多選檔案
```html
<input type="file" name="file" multiple>
```