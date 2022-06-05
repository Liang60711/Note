## 物件導向三大基本特性
* 封裝性 (encapsulation)
* 繼承性 (inheritance)
* 多型性 (polymorphism)

<hr>

### 封裝性 (encapsulation)
封裝就是把屬性封在類別中，可以將資訊隱藏 (information hiding) ，主要就是不讓外界隨意存取類別的屬性，只讓類別的屬性給同個類別的方法存取。




<hr>


### 繼承性 (Extends)
* 所有類別都是繼承 Object
* 目的是讓子類別 (subclass) 可以擁有父類別 (superclass) 的屬性 (field) 及方法 (method)。

<hr>

### 多型性 (Polymorphism)
* **繼承**是**多型**能夠實現的必要條件。
* 多型就為是原有的類別 (class) 提供一個**上層介面(Interface)**或**抽象類別(Abstrace class)**，之後對不同類別(class)做同一種操作，將得到不同的結果；目的是為了消除類別之間的耦合性，使程式更容易維護與擴充。
* 通常用在實踐抽象的介面(Interface)。
* 覆寫(override)、多載(overload)都是多型性的實作方法。
* 可用介面(Interface) 來宣告變數型態。

<hr>

### 抽象類別 (Abstract class)
在 Java 中定義類別時，可以僅宣告方法名稱而不實作當中的邏輯，這樣的方法稱之為「抽象方法」（Abstract method），如果一個方法中包括了抽象方法，則該類別稱之為「抽象類別」（Abstract class）。

* 抽象類別是擁有未實作方法的類別，所以它不能被用來生成物件。
* 只能被繼承(Extends)擴充，也因為只能被擴充，所以不得宣告為 final class。
* 要宣告抽象方法與抽象類別，要使用 "abstract" 關鍵字。
* 抽象類別不一定要有抽象方法，只要在類別宣告 abstract 即可。
```java
public abstract class Circle {          // abstract class
    protected double radius;
    public abstract void render();      // abstract method 可有可無
}
```


### 介面 (Interface)
* 定義屬性需皆為常數 (預設加上修飾子 public static final)，因此必須給定初始值。
* 定義方法時，只能為抽象方法 (預設加上修飾子 public abstract)，定義功能的名稱，實作部分留給相關類別 override。
* 只能被實作 (使用 implements)，子類別需實作抽象方法 (否則需宣告為抽象類別)
```java
public interface Circle {
    double radius = 5;              // 即 public static final double radius = 5
    void render();                  // 即 public abstract void render
}
```



<br>

||抽象類別 Abstract Class|介面 Interface|
|--|--|--|
|速度|快|慢|
|繼承|只能繼承(extends)一個類別|能實作(implements)多個介面|
|變數|有 final, non-final, static, non-static變數|只有 final static|
|結構|抽象方法或實作方法|抽象方法或deault and static 方法|
|修飾子|可有private protected|預設都是public| 
||Abstract class 可由 Interface 實作(implements)而來|Interface 不可由 Abstract class 實作而來|


<br/>

<br/>

在 Java8 中 interface 可以有實作方法，但須加關鍵字`default`，原因是為了讓 interface 有更好的擴充性，舉例，若 DataHelp 這個 class 新開發一個方法 getData()，會造成原本 implements 此 interface 的類報錯，故可使用 default method。

```java
public interface DataHelp {
    default List<String> getData(){
        // 加上 default 後，可以實作方法
        List<String[]> list = new ArrayList<String[]>();
        return list;
    }
}
```

<br>
<br>



### Extends 與 implements 的差別：
extends 用於類別(class)，implements 用於介面(interface)。


<br>
<br>
<br>
<br>
<hr>


### 泛型 (Generic)
為多型(Polymorphism)的一種技巧，當編譯期間無法確定程式碼的撰寫方式，不用因為資料型別的限制而實作多種方法，只需一種方法即可。
```java
// <T> 為關鍵字，可代表 int, double 等資料型別

public class number<T>{
    public void add(T num1, T num2){
        //...
    }
}

```


### 設計模式 (Design patterns)
* 將反覆出現的各種問題提出解決方案。
* GoF(Gang of Four design patterns)提出了23種經典設計模式。