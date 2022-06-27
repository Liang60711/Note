## application.properties 設定檔
1. 專案位置: `src/main/resources/application.properties`
2. 副檔名為`properties`，語法為 `key=value`，換行可以建立下個 key-value pair，有些專案副檔名則用`yml`。
3. comment 是使用 `#` 符號開頭。
4. 讀取設定檔的方式:

    使用`@Value("${key}")`來讀取，使用範圍為`Bean中的變數`或是`設定用class中的變數`，也就是`@Component或@Configuration中的變數`。

    ```properties
    # application.properties
    price=1000
    printer.name=MyPrinter
    printer.count=10
    ```
    ```java
    @Component
    public class MyBean{

        // 需注意類型是否符合
        @Value("${price}")
        private Integer price;

        @Value("${printer.name}")
        private String name;
        
        // 加上冒號可以設定預設值
        @Value("${unknown:foo}")
        private String name2;

    }
    ```
