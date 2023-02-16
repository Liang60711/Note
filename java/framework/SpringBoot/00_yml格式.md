## yml 格式

* 只允許使用空白鍵縮排，不允許使用Tab
*  `List` 格式範例，兩種寫法相同

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

* `String[]`，與 List 同格式
    ```yml
    functionmenu: >
      EC01
      EC02
      EC03
    ```
    ```yml
    functionmenu: EC01, EC02, EC03
    ```

    取值: 以上 Array 或 List 可以使用以下方式取

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

## yml 使用變數

使用 `${}` 符號來變數

```yml
baseDir: c:\windows

# 使用變數
tempDir: ${baseDir}\temp
```

<br/>

<br/>

## yml 支持逃脫字元
使用雙引號，即可解析逃脫字元
```yml
baseDir: c:\windows

# \t 可以被解析
tempDir: "${baseDir}\temp" # 取值會是 c:\windows emp
```