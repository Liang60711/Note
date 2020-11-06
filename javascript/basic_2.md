# functions
* 呼叫函式需加上左右小括號，若為回呼函式 callback 則不加 (僅用於傳遞參數，此時函式為物件)
* 若沒有 return 物件，則預設 return undefined
* 函式內的變數分為**區域變數**和**全域變數**，區域變數只能在function內使用。

```javascript
function <name>(<parameter1>,<parameter2>){
    //code
}
```
可設定 default
```javascript
function helloSomeone(name = "world"){
    return 'hello '+ name
}

// 若輸入helloSomeone()，輸出為 "hello world"
```
回呼函式 callback  
* 在函式執行完後才執行的函式
* 以參數型態傳入另一個函式的函式

```javascript
var company = ['A', 'B', 'C'];

function Good(x){
    console.log(x + ' good!')
}

// 使用 array.forEach(<callback>) 舉例
comapny.forEach(Good)     // A good! 'B good!' 'C good!'
```
callback 函式 加不加括號
```javascript
comapny.forEach(Good)
/*     
此時是把 Good 函式當作參數傳給 forEach()，參數為整個函式，但沒有執行
參數:
function Good(x){
    console.log(x + ' good!')
}
*/
```
```javascript
company.forEach(isGood())

/*
此時 Good() 執行完畢後，回傳 undefined 進 forEach()，此時參數為 undefined，故無法執行
*/
```


# array
* 屬於**複合資料型態composite data type**，可同時放入string, int, boolean, array(二維) 不同資料型態

宣告array
```javascript
var company = ['Apple', 'Alphabet', 'MicroSoft'];
```

讀取，index 無法用負數
```javascript
company[0]
// Apple

company[company.length-1]
// MicroSoft 取最後一筆
```

更改
```javascript
company[0] = 'Netflix'
// 直接更改value
```

取長度
```javascript
company.length

// 3
```
新增至最後一筆
```javascript
// 方法1
company[company.length] = 'Facebook'


// 方法2
company.push('Facebook')
```
刪除
```javascript
var company = ['Apple', 'Alphabet', 'MicroSoft'];

var lastOne = company.pop()

// pop 刪除最後一個值，並回傳
// lastOne = MicroSoft
// company = ['Apple', 'Alphabet']
```
for 取 array
```javascript
var company = ['Apple', 'Alphabet', 'MicroSoft'];

for (var i=0; i<company.length; i++){
    console.log(company[i])
}
```
forEach()  
此方法會為 array 中的每個元素呼叫一次 callback 函式
```javascript
var company = ['Apple', 'Alphabet', 'MicroSoft'];

function Good(x){
    console.log(x + ' good!')
}

company.forEach(Good)
// Apple good! Alphabet good! MicroSoft good!
```