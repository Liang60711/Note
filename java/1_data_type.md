## 變數

### 區域變數
1. 不可以提供給其他物件使用
2. 不可以提供給其他方法使用
3. 必須要給初始值
4. 沒有修飾子(因為不提供給其他物件和方法使用，也不需要修飾子)

<hr>
<br>

## 資料型態
|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|位元組|byte|8|-128 ~ 127|
|整數|short|16|-32768 ~ 32767|
|整數|int|32|-2^31 ~ 2^31-1|
|整數|long|64|-2^63 ~ 2^63-1|

<br>

|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|浮點數|float|32|精度7~8位|
|浮點數|double|64|精度16~17位|

<br>

|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|字元|char|16|None|
|布林值|boolean|1|true, false|

<br>

### char 在定義的時候，可以用以下方法給值:
1. 單引號
2. ASCII code

<br>

### Overflow 溢值
如果資料長度超過記憶體位元數，稱為Overflow

<hr>

## 型態轉換
Java中有兩種型態轉換的方法:  
**Widening Casting (自動轉換)**: converting a smaller type to a larger type size  
byte -> short -> char -> int -> long -> float -> double

**Narrowing Casting (需手動轉換)**: converting a larger type to a smaller size type  
double -> float -> long -> int -> char -> short -> byte

<br>

### 程式預設值
1. 整數預設值為 int
2. 浮點數預設值為 double
3. 所以要使用 short, long, float 需在數字後加上 s, l, f

<br>

### 字串 轉 基本資料型態
```java
double d = Double.parseDouble("3.14");
```
<br>

###  基本資料型態 轉 字串
```java
String s = String.valueOf(3.14);
```

<br>

### Double.parseDouble() 和 Double.valueOf() 差異
```java
String s = new String("3.14");

double d1 = Double.parseDouble(s);  // d1 是 double 基本資料型態

Double d2 = Double.valueOf(s);      // d2 是 Double 物件()

// valueOf 是先 parseDouble 再進行 autoBoxing，所以資料型態不一樣
```


<hr>

## 產生實體物件
### new 是產生實體物件的關鍵字
```java
Double d = new Double(3.14);
```

### 將基本資料型別轉換為物件(就可以使用物件屬性、方法)
```java
double d1 = 3.14;                // double 型別
Double d2 = Double.valueOf(d1);  // 物件

System.out.print(d2.intValue()); // 3
System.out.print(d2.SIZE);       // 64
```

### Java編譯器功能: 自動裝箱 AutoBoxing, UnBoxing
* AutoBoxing: 編譯器自動將**基本資料型別**轉換為**物件**。
* UnBoxing: 編譯器自動將**物件**轉換為**基本資料型別**。
* Compiler sugar
* 上方 d1, d2 不用 valueOf()