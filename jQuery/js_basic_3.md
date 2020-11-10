# if.. else if.. else
```java
if (<condition>) {
    //statement;
} else if (<condition>) {
    //statement;
} else {
    //statement;
}
```

# while
```java
while (<condition>) {
    //statement
}
```

# do while 
與while不同的是，迴圈至少被執行1次，再判斷條件
```java
do {
    //statement
} while (<condition>)
```

# for 
```java
for (<initialExp>;<condition>;<incrementExp>){
    //statement
}

// <initialExp>   初始條件
// <condition>    判斷條件
// <incrementExp> 更新條件
```
舉例
```javascript
for (var i = 0; i < 5; i++){
    console.log(i)
}
// 回傳 01234
```
for...in 和 for...of，對象為array  
for...in 輸出屬性名稱(key)，for...of 輸出值(value)
```javascript
var alpha = ['A','B','C']

for (i in alpha){
    console.log(i)
}
// 輸出 0 1 2


for (j of alpha){
    console.log(j)
}
// 輸出 A B C
```
舉例，對象為 object
```javascript
var myO = {a:'334455', b:'7788', c:'6699'}

for (k in myO){
    console.log(k)
}

// 輸出key: a b c
```

# DOC (Document Object Model)
* 是一個以樹狀結構來表示HTML文件的模型
* 每個 element, text 都是一個 node
* document 為整份文件的 root
* Attributes 是 html 元素上的屬性；Properties 是 DOM 物件上的屬性，不會影響到 html

## document Properties 屬性
```javascript
document.URL
document.body
document.head
document.links
```

## document method (正常情況會用jQuery)
```javascript
document.getElementById('<id>')                   // element沒加s，且回傳值為object，因為Id只有一個
document.getElementsByClassName('<className>')    // 回傳所有符合元素，HTMLCollection 型態，類似array型態，可迭代
document.getElementsBytagName('<tag>')            // (同上)

document.querySelector('<selector>')              // 回傳符合條件的第一個element
document.querySelectorAll('<selector>')           // 回傳符合條件的全部element
```

## object 物件
當 query node後，可將返回值存成 object 物件
```javascript
var x = querySelector('<selector>')
```
object Properties 和 method
```javascript
x.textContent           // 回傳物件text
x.innerHTML             // 回傳物件原始html
x.getAttribute()        // 回傳屬性
x.setAttribute()        // 重設屬性
```

## 使用原生 js 製作基本動態物件
1. 建立 js 物件
2. 建立 js 物件 eventListener 和 function  

## 舉例，滑鼠移動至 element 動畫
```javascript
// 1.建立物件
var one = document.querySelector('#one')

// 2.滑鼠移動至 element 動畫
one.addEventListener('mouseover', function(){
    one.textContent = '滑鼠移過來紅色';
    one.style.color = 'red';
})

// 3.滑鼠移走動畫
one.addEventListener('mouseout', function(){
    one.textContent = '原來的text';
    one.style.color = 'black';
})
```
addEventListener() 其他事件
```javascript
'click'     // 滑鼠點擊
'dblclick'  // 滑鼠雙擊
```