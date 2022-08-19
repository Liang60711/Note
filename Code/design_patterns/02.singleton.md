## 單例模式
- 保證一個類只能實例一個物件，並提供一個能訪問的全域訪問點。
- 適用: 
    1. 設計`工具類`的時候，因工具類通常沒有屬性，只有方法。
    2. 工具類可能會被頻繁調用。
    3. 為了節省重複建立物件時所帶來的記憶體消耗。
- 作法: 
    1. 建構子使用private(為了防止此類被二次實例化)。
    2. 建立一個類的物件(此物件為自己本身)。
    3. 給外部一個靜態方法，建立此物件。

- 記憶體分析: 
    1. 因為單例模式還是使用一般依賴物件(非靜態)的方法，在調用方法時，才會將方法丟進stack中，方法結束後釋放，故可減少記憶體資源占用。
    2. 如果是使用靜態方法(單例模式，像是`java.lang.Math`)，雖然調用效率高，但是更消耗記憶體資源。

<br/>

`eager積極模式`  
生命週期: 類被載入時，物件就被實例化，直到程序結束。  
缺點: 在多執行緒時，會有安全問題。
```java
public class Singleton{
    // 建立一個物件(自己，且只能被getInstance使用)
    private static Singleton instance = new Singleton();

    // 建構子使用private，所以要實例此物件只能透過getInstance()，達到實例化一次的作用
    private Singleton(){}

    public static Singleton getInstance(){
        return instance;
    }
}
```
`lazy模式`  
生命週期: 第一次調用getInstance()，物件才被實例化，直到程序結束(減少記憶體資源)。
```java
public class SingletonLazy{
    // 不先建立物件
    private static SingletonLazy instance;

    private SingletonLazy(){}

    // 判斷是否已建立物件
    public static SingletonLazy getInstance(){
        if (instance == null){
            instance = new SingletonLazy();
        }
        return instance;
    }

    // 寫一個測試方法
    public void print(){
        System.out.print("已建立物件");
    }

}
```