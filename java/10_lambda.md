## Lambda
* 定義函式介面實作的一種方式
* 不用建立實作的類別
* 不用輸入方法名稱
* 用法: 箭頭 `->` 前輸入引數，箭頭後輸入邏輯，與箭頭函數用法相同。

<br/>

舉例:
```java
// Comparator 為介面，compare，compare() 為未實作方法
Comparator<Integer> com = (x, y) -> x-y;
int ret = com.compare(5,2);
System.out.println(ret);
```
可以在 `ArrayList.forEach()` 使用 
```java
List<String> list = new ArrayList<String>();
list.add("A");
list.add("B");
list.add("C");

// lambda 函式與 callback 用法相同
list.forEach(item -> System.out.println(name);)
```
可以使用靜態方法(static method)來定義函式介面實作，但輸入與回傳參數需一致，並使用 `::` 來使用。
```java
// 相減的靜態方法
Math::subtractExact(5, 2);

// 可以參考以上方法，來實作
Comparator<Integer> com = Math::subtractExact; // 這邊方法不用加小括號

Integer result = com.compare(5, 2);
```