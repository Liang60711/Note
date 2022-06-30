## 枚舉類 Enum
1. 概念: 類的物件只有`有限個的`、`確定的`，常用在 `常量`定義上。
2. 舉例: 星期幾、性別、季節、訂單狀態。
3. 如果枚舉類只有一個物件，則可以做為`Singleton pattern` 的實作方式。

<br/>

<br/>

## 實作
枚舉類其實就是在類的內部new自己產生的物件，由於都是常數，故省略
```java
public enum Season {
    // 枚舉寫法，使用逗點隔開
    SPRING("春", 1),
    SUMMER("夏", 2),
    FALL("秋", 3),
    WINTER("冬", 4);
    /////////////// 其實就是以下的簡寫
    public static final Season SPRING = new Season("春", 1);
    public static final Season SUMMER = new Season("夏", 2);
    public static final Season FALL = new Season("秋", 3);
    public static final Season WINTER = new Season("冬", 4);
    /////////////////////////////////


    // 一樣有成員變數
    private final String name;
    private final Integer id;

    // Constructor
    Season(String name, Integer id) {
        this.name = name
        this.id = id;
    }
}
```

<br/>

<br/>

## 枚舉類方法
1. `values()` : 返回所有物件的陣列。

    ```java
    Season[] seasons = Season.values();
    ```

2. `valueOf(String enumName)` : 返回指定的枚舉物件。

    ```java
    Season s = Season.valueOf("SPRING");
    ```

3. `ordinal()` : 非靜態方法，返回枚舉物件的index。

    ```java
    Season s = Season.valueOf("SPRING");
    System.out.println(s.ordinal());// 0
    ```