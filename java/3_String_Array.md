


# Array
* 陣列只能存放相同資料型態；例如:String array
* 陣列是物件

<br>

### 陣列宣告1
```java
int[] array_1 = {1, 2, 4, 8, 16};
```

### 陣列宣告2
```java
int[] array_2 = new int[5];

// 宣告長度為5
```

### 二維陣列宣告1
```java
int[][] array_3 = {{1, 2}, {3, 4, 5}}       // 不一定要正方形矩陣

```

### 二維陣列宣告2
```java
int[][] array_4 = new int[2][2]     // 表示 2X2 int陣列

array_4[0][0] = 1;                  // 賦值
array_4[1][1] = 1;

array_4[2][2] = 1;                  // 超出範圍會報錯
```

### 陣列取值
```java
int a = array_1[0]      // a = 1
```

### 陣列複製
```java
int[] num1 = new int[2];
int[] num2 = Arrays.copyOf(num1, 2);    // 參數1=要複製的陣列，參數2=複製長度
```

不能使用
```java
int[] num2 = num1       // 這個意思是將 num1 的 reference 交給 num2
```

### 陣列 print
```java
// 因為 array是物件，所以需要轉型才能print
import java.util.Arrays;

String[] array = {"id", "name", "age"};

System.out.println(Arrays.toString(array))      // ["id", "name", "age"]
```

<br>

<hr>

# String
* 字串String 就是 字元char 的陣列。


### 字串宣告
```java
// 兩種等價
char[] c = {'a', 'b', 'c'};

String c = "abc";
```

### 字串取值1 - 取單字
```java
String str = "abc";
str.charAt(1)           // 輸出 b
```

### 字串取值2 - 取區間
```java
String str = "abcdef";
str.substring(0,4);     // 輸出 abcd
```


### 字串 split(), trim()
```java
String str = "id, name, age";           // 類似 csv 檔
String[] array = str.split(",");

System.out.println(array[1].trim());    // trim() 為去掉首尾空白
```

### 字串 索引值 indexOf(), lastIndexOf()
```java
// 通常會和 subString() 一起用
// indexOf()        回傳第一個字串
// lastIndexOf()    回傳最後一個字串

String str = "aabbccdd";
str.indexOf("a");           // 0
str.lastIndexOf("a");       // 1 
str.indexOf("ab");          // 1
str.indexOf("x");           // -1 找不到回傳-1
```


### 字串 檢查 contains()
```java
// 會回傳 boolean

String str = "abcdefg";
str.contains("a");          // true
str.contains("x");          // false
```

### 字串 檢查 startsWith(), endsWith()
```java
// 會回傳 boolean

String str = "abcdefg";
str.startsWith("a");        // true
str.endsWith("x");          // false
```


### 字串 取代 replace()
```java
// String 或 char 都適用，取代所有相同字串

String str = "abcd_abcd";
str.replace("a", "b");      // bbcd_bbcd
```

### 字串 取代 replaceAll()
```java
// 語法: replaceAll(regex, String)，取代所有相同字串

String str = "abc123";
str.replaceAll("\\d", "!");       // 跳脫字元 \\d
```

<br/>

## 字串/物件 檢查 == 和 equals() 差別
`==` 
* 為運算子。
* 對於8大基本資料型態，比對兩者的數值。
* 對於物件，則是比對兩者存放的記憶體位置。

`equals()`
* 為物件方法 (`java.lang.Object`類裡面的方法)。
* 純粹比較兩個物件是否有相同的值。

```java
String str1 = "abc";
String str2 = "abc";
String str3 = new String("abc");

(str1 == str2)      // true
(str1 == str3)      // false
str1.equals(str2)   // true
str1.equals(str3)   // true
```
解釋: 
1. `str1` 和 `str2` 指向相同的物件，擁有相同的address。
2. `str1` 和 `str2`，會指向相同物件的原因為，兩者在常數池(constant pool)中，為相同數值。


<br/>

<br/>

## *常數池 constant pool
>http://zake7749.github.io/2015/11/08/constantPool/

Java存在一個特殊空間，把編譯時期就已經確定的常數(constant)包進.class，執行時再從這個空間統一拿出。這個特殊空間稱作常數池或(constant pool)，它主要保管這兩種東西：

1. `字面量(Literal)`: 包含了常數以及既定字串。

2. `Symbolic References`: 主要是類別和介面的Fully qualified name，欄位和方法的名稱。

每當編譯完成，編譯器會將上述兩者包進.class中。原始碼中每一個字面量會被統一構成一張常數表，並透過索引的型式參照它。



```java
// 舉例: 

String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");
String s4 = "Hel" + "lo";
String s5 = "Hel" + new String("lo");
String s7 = "He";
String s8 = "llo";
String s9 = s7+s8;
String s10 = new String("Hello");


System.out.println(s1 == s2);   // true
System.out.println(s1 == s3);   // false
System.out.println(s1 == s4);   // true
System.out.println(s1 == s5);   // false
System.out.println(s1 == s9);   // false，這是因為編譯器不知道我們在String s9 = s7+s8;這行前，會不會對s7與s8的值進行修改，若貿然的假設s7仍是He或s8仍是llo，反而可能導致預期外的錯誤。
System.out.println(s3 == s10);  // false
```


常數池運作方式:

1. 編譯時發現字串，若常數池中已存在，則將現有的(先前找到的)位置賦予它，否則將該字串壓入常數池，並紀錄位置。

2. 當載入class時，JVM會尋找.class的常數表，發現了字串常量Hello，在heap中生成對應的實例。
3. 接著JVM會對常數池的入口位置進行解析，首先會找到字串s1的字面量Hello。
4. 由於常數池中已經存在Hello，所以提供常數池的引用給s1
5. 之後JVM找到s2的字面量Hello，
6. 由於常數池中已經存在Hello，所以仍舊將s2指向已經生成好的Hello，而非再產生一個Hello。