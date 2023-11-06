## String
String 類都是宣告為 final，並實現以下三個接口。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {

    private final char value[]; // Java 9 後改成 byte[] 型別
    private int hash; // Default to 0
    private static final long serialVersionUID = -6849794470754667710L;
    private static final ObjectStreamField[] serialPersistentFields =
        new ObjectStreamField[0];
}
```

表示 String 物件一旦被宣告，將無法被改變。

`String Table`，是一個在 JVM 中 heap 區塊中的一個 HashTable，用以作為 String Pool。

<hr/>

<br/>

<br/>

## 字串拼接效能

使用`StringBuilder` 類的 `append()` 的方式添加字符串的效率要`遠高於`使用 String 的字符串拼接(`+`)方式。

* StringBuilder的append()的方式： 自始至終中只創建過一個StringBuilder的對象。

* 使用 String 字串拼接(`+`)方式： 拼接方式底層也是透過上面StringBuilder.append()的方式實現，此過程會創建過多個 String 物件，內存佔用更大；如果進行GC，需要花費額外的時間(在拼接的過程中產生的一些中間字符串可能永遠也用不到，會產生大量垃圾字符串)。

    ```java
    // String name = "a" + "b";
    String name = new StringBuilder().append("a").append("b").toString();
    ```

<br/>

<br/>

## String.intern() 方法
調用此方法的時候，就是到常量池中查看此字符串是否存在於常量池中，如果存在的話，就直接返回地址的引用，如果不存在，就創建此字串常量，並返回地址的引用。

基本面試題
```java
String str1 = new String("Hello");  // 创建一个新的字符串对象，此字串不會進字符串池
String str2 = "Hello";  // 创建一个字符串，并放入字符串池
String str3 = str1.intern();  // 将str1字串对象放入字符串池，并返回引用

System.out.println(str1 == str2);  // false，因为它们不是同一个对象
System.out.println(str2 == str3);  // true，因为它们都指向了字符串池中的同一个字符串
System.out.println(str1 == str3);  // false，因為str1對象不屬於字符串池
```