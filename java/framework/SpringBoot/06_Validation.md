## 驗證資料 @Valid / @Validated
1. 設定 `pom.xml`，Spring Boot在 2.3 之後的版本都要手動加上 validation。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    ```
2. 取資料的4種方註解中，只有`@RequestBody`能用`@Valid`，其他都只能用`@Validated`
    * `@RequestBody`    : 使用@Valid

    * `@RequestParam`   : 使用@Validated
    * `@RequestHeader`  : 使用@Validated
    * `@PathVariable`   : 使用@Validated

<br/>

<br/>

3. `@Valid`，加在參數前，註解的順序沒差。

    ```java
    /**
     * 第一種，@Valid加在參數中，規則加在類中
     */
    @RestController
    public class StudentController{
        @PostMapping("/students")
        public String create(@RequestBody @Valid Student student){
            return "create";
        }
    }
    ```
    ```java
    public class Student {

        @NotNull    // 添加在成員變數
        Integer id;
        String name;
    }
    ```

4. `@Validated`，加在class上，針對 Bean 去做校驗。
 
    ```java
    /**
    * 第二種，@Validated加在類上，規則加在參數前
    */
    @RestController
    @Validated
    public class StudentController{
        @PostMapping("/students")
        public String create(@RequestBody @NotNull Student student){//規則加在參數前
            return "create";
        }
    }
    ```

    

5. 其他規則 


    |驗證註解|說明|只能對以下類型使用|
    |--|--|--|
    |@NotNull|不可為null||
    |@NotBlank|不可為空白字串(空白鍵也不行)|String|
    |@NotEmpty|不能為null，且size需 >0|Collection|
    |@Min(value)|驗證數字必須 >= value|Integer|
    |@Max(value)|驗證數字必須 <= value|Integer|
    |@Size(min, max)|字串長度/集合的size，必須在min, max範圍內(小於等於、大於等於)|String, Collection|
    |@Email|必須符合Email格式|String|
    |@Pattern(regexp)|必須符合正規表達式regexp||
    |@Past|比當前時間早||
    |@Future|比當前時間晚||
    |@AssertTrue|必須為True|boolean|
    |@AssertFalse|必須為False|boolean|
    |@Null|必須為Null||


<br/>

<br/>

6. package名稱為 `javax.validation.constraints`。

7. 常使用在 @Configuration 的校驗上，是針對 yml 的值做校驗，這樣啟動 springboot 就會報錯，並提示錯在哪。

    ```java
    @Configuration
    @ConfigurationProperties(prefix = "servers")
    @Validated
    public class ServletConfig {

        @Max(8888)
        private int port;
    }
    ```