## adapter 適配器 轉接器

<img src="https://3.bp.blogspot.com/-vjWnaHjv5s4/XJ4UQkkX27I/AAAAAAAAAOo/72U6nK_ZZxMT6_zeEXMyByuAWm2J1SRZACLcBGAs/s1600/19329_1.JPG" style="20%">

1. `adapter pattern`: 當客戶端想用一個已經有的class功能，但這個class的interface與客戶端所用的型態不同，可以使用`adapter pattern`解決。

2. `adapter` 成員:
    * Target:  被Client使用的物件介面(interface)，此介面的物件想利用既有的Adaptee實作物件的某個功能。
    * Adaptee: 被轉接的介面(interface)，其既有實作的功能需要被Target使用。
    * Adapter: 轉接器類別(class)，用來轉接Target與Adaptee。


3. 舉例 : interface A 有許多5個抽象方法，B class 實作 A 時只需 method1, 2 時，就必須多覆寫 method3, 4, 5 空方法，

    ```java
    // 抽象層
    interface A {
        public method1();
        public method2();
        public method3();
        public method4();
        public method5();
    }

    // 解決方式: 
    // 適配器 (先實作將這5個方法成為空方法)
    class Adaptor implements A {
        // override method 1,2,3,4,5....
    }

    // 繼承的子類再去從 適配器 中覆寫自己要實作的方法
    class B extends Adaptor {
        // override method 1,2
    }
    ```


4. Java8 更新後，interface 出現 default method，因此適配器使用幅度降低。