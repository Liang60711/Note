


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


### 字串 檢查 equals()
```java
// 會回傳 boolean
// equals() 是檢查數值， == 是檢查 reference 位址
// new 會建立新物件(新reference)

String str1 = "abc";
String str2 = "abc";
String str3 = new String("abc");
String str4 = new String("abc");

str1.equals(str2)       // true
str1 == str2            // true
str3.equals(str4)       // true
str3 == str4            // false
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