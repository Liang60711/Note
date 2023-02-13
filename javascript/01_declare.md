## 基本概念
* var
* let
* const
* 無關鍵字宣告

|宣告類型|範圍|備註|
|--|--|--|
|var|函數|定義一塊記憶體給此變數，在全域中宣告為`全域變數`，在函數內宣告為`區域變數`|
|let|區域|大括號內有效|
|const|區域|大括號內有效|
|無宣告|全域|從`區域`往`全域`尋找變數，未找到就建立成全域變數，找到就覆蓋原宣告值|

<br/>

<br/>

### var
var 宣告時，可以被覆蓋
```javascript
var i = 0;
var i = 1;
var i = 2;
console.log(i)  // 2
```
var 可以被覆蓋 (var不嚴謹的地方)
```js
var i = 0;

// ....

// 若沒注意到，再宣告了一次
var i = 1;
```

<br/>

<br/>

## const
為final，不可再更動值
```js
const i = 0;

function foo() {
    i = 1;  // 報錯，'i' has already been declared
}
```

<br/>

<br/>

## let, const 
用此兩種方式宣告的變數，不會出現在 window 中，只有 var 宣告的變數，才會出現在 window 中。

<br/>

<br/>

## 基本型態是傳值，物件是傳址

```javascript
var a = 10;
var b = a;

b = 20;
console.log(a); // 還是10
console.log(b); // 20
```
物件是直接指向原本物件
```javascript
var m = {a: 10, b: 20};
var n = m;

n.a = 15;
console.log(m.a); // 15，因為 m, n 為同一個物件
```