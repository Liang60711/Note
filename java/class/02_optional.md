## Optional
* 可以為null的容器物件，如果值存在則`isPresent()`方法會回傳true，使用`get()`方法會回傳該物件。

* Java8引入，主要解決的問題是很常見的`NullPointerException`。

<br/>

<br/>

## 相關方法

`of()` 建立一個非null值的Optional物件。
```java
Optional<String> opt = Optional.of("bin");
```

`ofNullable` 建立一個可以為空的Optional物件。
```java
Optional<String> opt = Optional.ofNullable(null);
```

`empty()` 建立一個空物件
```java
Optional<String> opt = Optional.empty();
```

`isPresent()` 檢查是否Optional物件為空，非靜態方法
```java
Optional<String> opt = Optional.of("bin");
opt.isPresent();//true
```
`get()` 如果有值，則返回該物件。
```java
opt.get();
```
`ifPresent()` 如果有值，則執行一個`Consumer`，沒值就不處理。
```java
opt.ifPresent(System.out::println);
opt.ifPresent((value) -> System.out.println(value));
```
`orElse` 如果物件存在，將其值返回，否則返回指定的其他值。
```java
optEmpty.orElse("無值");
```
`orElseGet()` 跟`orElse`差不多，只是參數會放`Supplier`。
```java
optEmpty.orElseGet(()->"default");
```
`orElseThrow()` 如果有值則返回，否則拋出`Supplier`建立的異常。
```java
Optional<String> opt2 = Optional.empty();

try{
    opt2.orElseThrow(Exception::new);
}catch (Exception e){
    e.printStackTrace();
}
```
`map()` 如果有值，執行mapping並得到操作後的值，`返回值需要用Optional物件去接`。
```java
Optional optMap = opt.map((value) -> value.toUpperCase());
```
`fiter()` 如果有值，且滿足`Predicate`條件，則返回符合條件的值，否則返回空。
```java
Optional filter = opt.filter((value) -> value.length() > 3);

System.out.println(filter.orElse("empty!"));
```
