## Reflection 反射
1. 反射允許程式在執行期間，使用 `Reflection API` 取得任何類的內部資訊，並能直接操作任意物件內部的屬性及方法。

2. 反射是讓 Java 有動態語言特性的關鍵。

3. 相關API路徑

    * `java.lang.Class` : 類
    * `java.lang.reflect.Method` : 取類的方法
    * `java.lang.reflect.Field` : 取類的變數
    * `java.lang.reflect.Contructor` : 取類的建構子

4. 使用new建立物件 / 使用反射建立物件，開發中用哪一個?

    * 一般建議直接使用new去建立物件。
    * 若在編譯時還不能確定要new哪一個類時(保留動態性)，則使用反射。

5. 反射機制是否跟物件導向的封裝性是否矛盾?

    * 封裝性的訪問權限比較像是"建議使用的方式"，通常不會硬要去取得private的結構；若非要暴力取得private的結構，還是可以使用反射。


* 補充: 動態/靜態語言

    * 動態語言(弱型別): 在運行時可以改變其結構的語言，例如新的函數、物件、型別，簡單來說: `在運行時程式可以根據某些條件改變自身的結構`，如PHP、Javascript、Python。

    * 靜態語言(強型別): 運行時結構不可變的語言，如Java、C、C++。

    * Java 可利用反射機制獲得類似動態語言的特性。

<br/>

<br/>

## 取得"運行時類"的方法
先講結論，`運行時類 == 物件`。

`運行時類`為`.class檔案`載入到記憶體中的物件，可以實例化該類的物件。

1. `getClass()`: 通過物件取

    ```java
    Dog dog = new Dog();
    Class clazz1 = dog.getClass();
    ```

2. `class` : 調用"運行時類"的屬性。

    ```java
    Class clazz2 = Dog.class;
    ```

3. `Class.forName()` : 調用"運行時類"的靜態方法，需寫完整路徑

    ```java
    Class clazz3 = Class.forName("com.example.demo.Dog")
    ```
4. 不管哪種方式獲得的"運行時類"，都是同一個物件，故，`記憶體中的"運行時類"只會被載入一次`。

    ```java
    System.out.println(clazz1 == clazz2);//true
    System.out.println(clazz1 == clazz3);//true
    ```


<br/>

<br/>

## 取類的各種資訊
```java
public class ReflectionTest {
    public void show() {

        // 先取類的物件
        Class clazz = Person.class;

        // 1. 取constructor
        Constructor cons = clazz.getConstructor(String.class, int.class);

        // 2. 透過建構子物件，實例化Person物件
        Object obj = cons.newInstance("Tom", 12);//newInstance回傳類型是Object，需強制轉型
        Person p = (Person) obj;

        // 3.取指定屬性/所有屬性
        Field age = clazz.getDeclaredField("age");
        Field[] fields = clazz.getDeclaredFields();

        // 4. 取指定方法(包含此類的public/protected/default/private方法)
        Method test1 = clazz.getDeclaredMethod("test1");
        test1.invoke(p);

        // 5. 取所有public方法(包含父類繼承來的)，沒找到會回傳null
        Method test2 = clazz.getMethod("test2");
        test2.invoke(p);

        // 以下為調用private的結構
        // 1.建構子
        Constructor cons1 = clazz.getDeclaredConstructor(String.class);//指調用private
        // 2.建立物件
        cons1.setAccessible(true);
        Person p1 = (Person) cons1.newInstance("Jerry");//向下轉型Object->Person
        // 3.私有屬性從外部更改
        Field name = clazz.getDeclaredField("name");
        name.setAccessible(true);
        name.set(p1, "newName");
        // 4.私有方法
        Method test2 = clazz.getDeclaredMethod("test2");
        test2.setAccessible(true);
        String str = (String) test2.invoke(p1,"hello"); // invoke也是回傳Object類型
        // Method.invoke(Object, param);
    }
}
```

```java
public class Person {

    /**
     * 成員變量
     */
    private String name;
    public int age;

    /**
     * constructor
     */
    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    private Person(String name) {
        this.name = name;
    }

    /**
     * 方法
     */
    public void test1() {
        System.out.println("test1");
    }

    private String test2(String str) {
        System.out.println("test2");
        return str;
    }
}
```

<br/>

<br/>

## 類的載入過程
1. 程式經過 `javac` 編譯過，會產生一個 bytecode檔案，也就是 `.class` 結尾的檔案，這個步驟為編譯。
2. 使用 `java` 命令對 `.class`文件進行解釋運行，相當於將`.class`文件加載到記憶體中。


3. 載入到記憶體中的類，此時記憶體中這個"運行時類"就會作為 Class 的物件。

    ```java
    // clazz就是"運行時的類"
    Class clazz = Person.class;
    ```

* 補充: 
    * `src code ====> byte code ====> machine code`
    * src code 透過 javac 的編譯成為中介檔案byte code
    * byte code 經過 jvm 的直譯成為 machine code
    * java非純的編譯語言，而是折衷，所以java會比純編譯語言(C, C++)來的慢一些，但是卻又擁有直譯語言的特性。

    > https://openhome.cc/Gossip/JavaEssence/WhyJVM.html