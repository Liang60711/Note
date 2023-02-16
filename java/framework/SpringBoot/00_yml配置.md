## 配置這麼多要去哪裡看

<img width="80%" src="https://user-images.githubusercontent.com/63166397/216974852-f330faef-b621-43f2-871b-48dc6b665901.png">

<br/>

yml屬性配置在第一個連結
<img width="80%" src="https://user-images.githubusercontent.com/63166397/216974870-9c68af6d-6881-467d-903b-37d8bc6ea089.png">

<br/>

<br/>

## 配置觀念
* 有載入相對應的依賴包，才能使用相對應的屬性配置
* 如果 properties, yaml, yml 三個檔案同時存在，`properties`最優先，`yml`次之，`yaml`最後。

<br/>

<br/>

## yml 格式

* 只允許使用空白鍵縮排，不允許使用Tab
*  list格式範例，兩種寫法相同

    ```yml
    # 為 list<String>
    users: >
      apple
      foo
      boo
    ```
    ```yml
    users: apple, foo, boo
    ```

* String[]，與 List 同格式
    ```yml
    functionmenu: >
      EC01
      EC02
      EC03
    ```
    ```yml
    functionmenu: EC01, EC02, EC03
    ```

* 以上 Array 或 List 可以使用以下方式取

    ```java
    @Value("${users:}")
    private List<String> userList;

    @Value("${functionmenu:}")
    private String[] array;
    ```
* 取 `List (物件寫法)`，使用物件的方式載入 yml 檔案中的 value
    ```yml
    cuztomize: 
      users:
        - apple
        - ornage
        - foo
        - boo
    ```
    ```yml
    # 行內寫法，與上相同
    cuztomize: 
      users: [apple, ornage, foo, boo]
    ```
    取值(需使用物件)
    ```java
    @Component
    @Data
    @ConfigurationProperties(prefix = "cuztomize.users") // 配置文件的前綴
    public class Users {

        private List<String> users;
    }
    ```

* 使用 `~`，表示 `null`

    ```yml
    null: ~
    ```
* 日期使用 `yyyy-MM-dd`格式
    ```yml
    date: 2023-02-15
    ```

<br/>

<br/>

## 在 IntelliJ 新增配置檔案
如果遇到 IntelliJ 在配置檔中沒有開啟 auto complete，原因是因為 IntelliJ 不認為此檔案室配置檔，所以需要去設定成配置檔案。
<img width="80%" src="https://user-images.githubusercontent.com/63166397/218733417-4ae4d133-02ae-4271-9f42-932c23e12e02.png">

<img width="80%" src="https://user-images.githubusercontent.com/63166397/218733427-2eed0a23-094b-4e02-a7fd-51ccfba16fcc.png">


<br/>

<br/>

## 靜態資源配置

* 靜態資源的訪問，默認是 `/resources/**`，如果有 `webapp/WEB-INF` 目錄，則使用此路徑作為根目錄。

* 源碼在 `WebMvcAutoConfiguration` 這個自動配置類中。

* 補充: 如果`API路徑(controller route)`與靜態資源相同，則會先去訪問`API路徑`(硬要測試的話)。



```yml
spring:
    mvc:
        # 使用路徑前綴
        static-path-pattern: /prefix/** #這樣訪問路徑即為/prefix/resources/**
    
    web: # <-- 舊版本沒有web階層
        resources:
            # 更改靜態資源路徑根目錄(所以根目錄為resources/rootPath)
            static-locations: classpath:/rootPath/
            # 禁用靜態資源路徑配置，效果就是所有的靜態資源都無法訪問
            add-mappings: true  #預設為true，禁用為false
            # 設置靜態資源緩存時間(11000秒)
            cache:
                period: 11000
```

<img src="https://user-images.githubusercontent.com/63166397/204546300-859ff0f9-d002-4ffb-a2d6-257171e4e38a.png" width="50%">

有了Cache後，Status code 為304，並且Request Header會產生 If-Modified-List

<img src="https://user-images.githubusercontent.com/63166397/204546386-c252e9d5-c644-48cb-b858-5b9666fbaf40.png" width="50%">


<br>

<br/>

## Log  配置
```yml
logging:
    level:
        root: info  # 預設是info級別，可調整為debug, error
```

<br/>

<br/>

