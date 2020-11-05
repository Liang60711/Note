# 基本規則
* **忽略空白字元**，所以可自由縮排讓code更好讀。
* **區分大小寫**，函數、變數、物件名稱都有區分。
* **以分號；作為語句結束**
* **Camel Case** 駝峰式命名


# 變數
### 宣告變數
```javascript
var varName = Value;
```
### null 和 undefined 區別  
* **null**: 變數已宣告，值為空  
* **undefined**: 變數尚未宣告，或是變數已宣告但尚未定義value


# 註解
### 單行註解
```javascript
//comment
```
### 多行註解
```javascript
/*
comment
in many
rows
*/
```

# 比較運算子 Operators
|運算子|說明|
|--|--|
|==|相等就回傳true，會嘗試轉型態，"2" == 2 會回傳true|
|===|data type 和 value 皆相等會回傳 true，"2" === 2 會回傳false|
|!=|不相等，會轉型態|
|!==|不相等，不會轉型態|

<br>

舉例
```javascript
true == 1;  // true 
true === 1; // false
```
```javascript
null ==  undefined   //true
null === undefined   //false
NaN  ==  NaN         //false
```



# 算數運算子 Arithmetic Operators
|運算子|說明|
|--|--|
|+-*/|加減乘除|
|%|餘數|
|++|**一元運算子**將變數值+1|
|--|**一元運算子**將變數值-1|

<br>

舉例
```javascript
var x = 10
x++            // 先回傳10，再將x值+1成為11
++x            // 先將x值+1成為11，再回傳11
               // 差別在順序
```

<br>

# 邏輯運算子 Logical Operators
|運算子|說明|
|--|--|
|&&| and |
|\|\| |or|
|!|NOT，可疊加| 
舉例
```javascript
! (1 === 1)     // false
!!(1 === 1)     // true
```

