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
    @ConfigurationProperties(prefix = "cuztomize") // 配置文件的前綴
    public class Cuztomize {

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

## 一次讀取 yml 全部屬性
使用 `Environment` 類封裝所有屬性，並使用自動注入
```java
@Autowired
private Environment env;
```
並使用 `getProperty` 取值，方式與 @Value相同
```java
String str = env.getProperty("spring.datasource.url");
```

<br/>

<br/>

## 使用物件載入指定的屬性
使 spring 只載入指定的屬性，不全部載入
```yml
datasource:
  driver-class-name: com.mysql.jdbc.Driver
  url: jdbc:mysql://localhost:3306/myjpa
  username: root
  password: root
```
直接寫一個載入類
```java
// 需符合三個條件
// 1.定義屬性名稱(需和yml屬性名稱一模一樣)
// 2.需成為 bean
// 3.指定前綴
@Component
@ConfigurationProperties(prefix = "datasource")
public class MyDataSource {
    private String driver-class-name;
    private String url;
    private String username;
    private String password;
}
```

<br/>

<br/>

## yml 使用變數

使用 `${}` 符號來表示變數

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

<br/>

<br/>

## yml 支持隨機數字
使用 `${random}`
```yml
my:
  secret: "${random.value}"
  number: "${random.int}"
  bignumber: "${random.long}"
  uuid: "${random.uuid}"
  number-less-than-ten: "${random.int(10)}"
  number-in-range: "${random.int[1024,65536]}"
```

<br/>

<br/>

## yml 支持 8進制/10進制/16進制
數字類型解析在某些情況下會出現問題，解決方法是加上雙引號
```yml
# yml
password: 0127  # 10進制是0127，但8進制就會被解析成87
```
造成以上原因: 8進制是`0`作為前綴，16進制是`0x`作為前綴