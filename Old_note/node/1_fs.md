# File System
## 讀取檔案
非同步，不會等處理完成再進行下一步
```javascript
var fs = require('fs');

fs.readFile('hello.txt', function(err, data){
    if(err){
        console.log('err')
    }else{
        console.log(data.toString())
    }
})
```
同步，照程式碼順序執行
```javascript
var fs = require('fs');

var data = fs.readFileSync('hello.txt', 'utf8')
console.log(data)
```
## 寫入檔案
同步寫入
```javascript
var fs = require('fs')

var str = "hello"
fs.writeFileSync('test2.txt', str)
```
非同步寫入，但要注意文字處理結束再寫入，否則會寫入不全。
```javascript
var str = 'hello'

// writeFile(file, text, callback)
fs.writeFile('test.txt', str, (err) => {
    if (err){
        console.log(err)
    }else {
        console.log('success!')
    }
})

console.log('complete!!!!!!!')

// 輸出順序
// complete!!!!!!!
// success!
```
writeFile 會將檔案重新覆蓋，而 appendFile 則會附加上去
```javascript
fs.appendFile('test.txt', 'hello world!!\n', (err) => {
    if (err){
        console.log(err)
    }else {
        console.log('success!')
    }
})
```
## 開啟檔案
1. <code>fs.open(path, flags[, mode], callback)</code>
2. <code>fs.read(fd, buffer, offset, length, position, callback)</code>

* fd: 透過 fs.open() 方法返回的文件描述符。
* offset: 緩衝區寫入的寫入偏移量。
* length: 要從文件中讀取的字元數。
* position: 文件讀取的起始位置。
* callback: 回呼函式，三個參數err, bytesRead, buffer。
err 為錯誤訊息， bytesRead 表示讀取的字元數，buffer 為緩衝區對象。
```javascript
fs.open('test.txt', 'r', (err, fd) => {
    if (err) {
        console.log(err)
    }

    var buffer = new Buffer(1024);
    fs.read(fd, buffer, 0, buffer.length, 0, (err, bytes) => {
        if (err) {
            console.log(err)
        }

        if(bytes > 0){
            console.log(buffer.slice(0, bytes).toString())
        }

        fs.close(fd, (err) => {
            if (err){
                console.log(err)
            }
        })
    })
})
```

## 刪除檔案
```javascript
fs.unlink('test.txt', (err) => {
    if(err){
        console.log(err)
    }
})
```