## 變數

### 區域變數
1. 不可以提供給其他物件使用
2. 不可以提供給其他方法使用
3. 必須要給初始值
4. 沒有修飾子(因為不提供給其他物件和方法使用，也不需要修飾子)

<hr>
<br>

## 整數
|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|位元組|byte|8|-128 ~ 127|
|整數|short|16|-32768 ~ 32767|
|整數|int|32|-2^31 ~ 2^31-1|
|整數|long|64|-2^63 ~ 2^63-1|

>通常 byte 會用在影片或照片處理

<br/>

<br/>

## 浮點數

|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|浮點數|float|32|精度7~8位|
|浮點數|double|64|精度16~17位|

<br/>

<br/>

## 字元、布林

|型態類型|關鍵字|位元數(bit)|範圍|
|--|--|:--:|--|
|字元|char|16|None|
|布林值|boolean|1|true, false|

<br>

### 雙引號和單引號
1. 單引號為 `char` 型別使用。
2. 雙引號為 `String` 類別使用。
3. 字元可以使用 ASCII。
4. 舉例: 

    ```java
    // char為型別
    char c = 'char';
    // String為類
    String s = "s";
    s.charAt(0);

    // char 可以使用 ASCII 命名。
    char c = 65;
    System.out.println(c); // A
    ```


<br>

### Overflow 溢值
如果資料長度超過記憶體位元數 (定義為short 卻給128的數值)，稱為`Overflow`。

<br>

## 型別轉換
有兩種型別轉換的方法: 

**Widening Casting (自動轉換)**: converting a smaller type to a larger type size  
`byte -> short -> char -> int -> long -> float -> double`

```java
int i = 9;
double d = i;   // 自動轉換

System.out.println(i);  // 9
System.out.println(d);  // 9.0
```

**Narrowing Casting (需手動轉換)**: converting a larger type to a smaller size type  
`double -> float -> long -> int -> char -> short -> byte`

```java
double d = 9.0;
int i = (int) d; // 手動轉換
```

<br/>

<br/>

<hr>

## 基本型別的預設值
1. 整數預設值為 `int`
2. 浮點數預設值為 `double`
3. 所以要使用 short, long, float 需在數字後加上 s, l, f
4. 舉例:
    ```java
    // 需在數值最後加上 l(L大小寫都可以)，否則編譯器預設是 int，會報錯。
    long l = 2147483648L;
    ```

<br>

<br>

## 型別 和 物件之間的轉換

### 型別轉物件
```java
double d1 = 3.14; // d1為型別
Double d2 = Double.valueOf(d1); // d2為物件
Double d2 = d1; // autoboxing
```

> Java 編譯器功能: 自動裝箱 AutoBoxing, UnBoxing。
* AutoBoxing: 編譯器自動將`基本資料型別`轉換為`物件`。
* UnBoxing: 編譯器自動將`物件`轉換為`基本資料型別`。
* Compiler sugar
* 上方 d2 其實不用 valueOf()。

<br>

### String物件轉型別 (parseDouble, parseInt)
```java
// String物件 轉 double型別
double d = Double.parseDouble("3.14");
```
<br>

### 型別轉String物件 (valueOf)
```java
// double型別 轉 String物件
String s = String.valueOf(3.14);
```

<br/>

<br/>

## 基本面試題
Flyweight 輕量模式(享元模式): Interger在`-128~127`之間有常量池存在，API位置在`java.lang.Integer`的`IntegerCache`靜態類中。
```java
Integer x1 = new Integer(1);
Integer x2 = new Integer(1);
System.out.println(x1==x2);//false
System.out.println(x1.equals(x2));//true

Integer x3 = new Integer(128);
Integer x4 = new Integer(128);
System.out.println(x3==x4);//false
System.out.println(x3.equals(x4));//true

Integer x5 = 1;
Integer x6 = 1;
System.out.println(x5==x6);//true
System.out.println(x5.equals(x6));//true

Integer x7 = 128;
Integer x8 = 128;
System.out.println(x7==x8);//false，常量池範圍以外
System.out.println(x7.equals(x8));//true
```