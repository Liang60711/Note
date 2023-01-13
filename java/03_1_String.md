# String
* 字串String 就是 字元char 的陣列。


### 字串宣告
```java
// 兩種等價
char[] c = {'a', 'b', 'c'};

String c = "abc";
```

### charAt() - 取單字
```java
String str = "abc";
str.charAt(1)           // 輸出 b
```

### substring() - 取區間
```java
String str = "abcdef";
str.substring(0,4);     // 輸出 abcd
```


### split(), trim()
```java
String str = "id, name, age";           // 類似 csv 檔
String[] array = str.split(",");

System.out.println(array[1].trim());    // trim() 為去掉首尾空白
```

### indexOf(), lastIndexOf() 取索引
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


### contains() 檢查是否包含
```java
// 會回傳 boolean

String str = "abcdefg";
str.contains("a");          // true
str.contains("x");          // false
```

### startsWith(), endsWith() 檢查前綴/後綴
```java
// 會回傳 boolean

String str = "abcdefg";
str.startsWith("a");        // true
str.endsWith("x");          // false
```


### replace() 取代 
```java
// String 或 char 都適用，取代所有相同字串

String str = "abcd_abcd";
str.replace("a", "b");      // bbcd_bbcd
```

### replaceAll() 取代(正則)
```java
// 語法: replaceAll(regex, String)，取代所有相同字串

String str = "abc123";
str.replaceAll("\\d", "!");       // 跳脫字元 \\d
```

### isEmpty() 判斷是否為空字串
```java
String str = "";
str.isEmpty();
```

### length() 回傳長度
`String`的length()是方法；`Array`的length是屬性
```java
String str = "aaa";
str.length();
```

### toLowerCase() / toUpperCase() 轉大小寫
```java
String str = "Aaa";
str = str.toLowerCase();
str = str.toUpperCase();
```

### concat() 合併字串
```java
String str = "aaa";
str = str.concat("---");
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

Java存在一個特殊空間(記憶體Heap中)，把編譯時期就已經確定的常數(constant)包進.class，執行時再從這個空間統一拿出。這個特殊空間稱作常數池或(constant pool)，它主要保管這兩種東西：

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
final String s11 = "He";
final String s12 = "llo";
String s13 = s11+s12;
String s14 = getString();
String s15 = "llo";
String s16 = s14+s15;


System.out.println(s1 == s2);   // true
System.out.println(s1 == s3);   // false
System.out.println(s1 == s4);   // true
System.out.println(s1 == s5);   // false
System.out.println(s1 == s9);   // false，如果在編譯期間值可以被確定(如s4)，則使用已有的物件(常量池)，但因s7+s8接為變數，在runtime運行期時，才能確定s9的值，故s9會另外建立物件。
System.out.println(s3 == s10);  // false
System.out.println(s1 == s13);  // true，與s9原因相同，因為常數(加上final)已能在編譯期間確定值，故s13使用常數池中的物件。
System.out.println(s1 == s16);  // false，方法在運行期才能返回值，故會新建立物件。

// 結論: 編譯期間可以確定的變數，都會從常數池撈。
```
```java
private static String getString(){
    return "He";
}
```


常數池運作方式:

1. 編譯時發現字串，若常數池中已存在，則將現有的(先前找到的)位置賦予它，否則將該字串壓入常數池，並紀錄位置。

2. 當載入class時，JVM會尋找.class的常數表，發現了字串常量Hello，在heap中生成對應的實例。
3. 接著JVM會對常數池的入口位置進行解析，首先會找到字串s1的字面量Hello。
4. 由於常數池中已經存在Hello，所以提供常數池的引用給s1
5. 之後JVM找到s2的字面量Hello，
6. 由於常數池中已經存在Hello，所以仍舊將s2指向已經生成好的Hello，而非再產生一個Hello。


<br/>

<br/>

## StringBuffer
* 為了減少字串在相加時所產生的物件，可以使用StringBuffer作為替代。
* 線程安全的(表示多線程時，會有lock機制)，效能低，適合在多線程中使用。

```java
// String
String a = "a";
String b = "b";
String c = a+b+"c";
System.out.println(c);//產生超過3個物件


// StringBuffer
StringBuffer sb = new StringBuffer();
sb.append("a").append("b").append("c");//只產生3個物件
System.out.println(sb.toString());
```

<br/>

<br/>

## StringBuilder
* 此類提供與StringBuffer兼容的API，被設計為StringBuffer的簡易替換，用在字串單線程時使用，效能會比StringBuffer高。
* 線程不安全的(沒lock機制)，效能高，適合在單線程中使用，這種情況佔大多數。
