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

<br/>

<br/>

## 設定不同開發環境的資料庫
1. `main/resources`目錄下建立不同環境的設定檔
    * application-dev.properties，開發用設定檔
    * application-dev2.properties，開發用設定檔

2. IntelliJ >> 點選右上角下拉選單 >> Edit Configurations >> 左側點選 Spring Boot >> 點選專案 >> `Active Profiles 填上 dev`
3. 上述操作是設定使用 dev 環境的設定檔。
