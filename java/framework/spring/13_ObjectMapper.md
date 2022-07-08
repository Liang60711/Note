## ObjectMapper 類
* Java Object 和 JSON 之間的轉換
* 若 Java 物件沒有設定的變數，則會被轉成 null
* `writeValueAsString()`: java 物件轉換為 json

    ```java 
    @GetMapping("/convert")
    public String convert() throws JsonProcessingException {

        // 1.建立java物件
        User user = new User();
        user.setId(1);
        user.setName("JJJ");

        // 2.使用writeValueAsString做轉換
        ObjectMapper objectMapper = new ObjectMapper();
        String jsonResult = objectMapper.writeValueAsString(user);
        System.out.println(jsonResult);

        return "convert success";
    }
    ```

* `readValue` : json 轉換為 java 物件
    
    若 json 中有 java物件沒有的變數，則回找不到變數來映射，故返回500

    ```java
    @GetMapping("/convert")
    public String convert() throws JsonProcessingException {

        ObjectMapper objectMapper = new ObjectMapper();

        String json = "{\"id\":1,\"name\":\"JJJ\"}";

        // 參數1 : json字串
        // 參數2 : 要轉換class
        User userResault = objectMapper.readValue(json, User.class);

        return "convert success";
    }
    ```

<br/>

<br/>

* `@JsonInclude` : 可過濾轉換後的 json

    ```java
    /**
     * 排除掉值等於 null 的數據
     */
    @JsonInclude(JsonInclude.Include.NON_NULL)
    public class User {
        //...
    }
    ```

* `@JsonIgnoreProperties(ignoreUnknown = true)` : 忽略 json 中 java物件沒有的變數。

    ```json
    {
        "id": 1,
        "name": "JJJ",
        "age": 22
    }
    ```

    ```java
    /**
     * 若 json 有 age 這個值，可使用此註解忽略，否則會報錯
     */
    @JsonIgnoreProperties(ignoreUnknown = true)
    public class User {
        
        Integer id;
        String name;
    }
    ```

* `@JsonProperty()` : 可指定 json 字串中的 key 和 java變數的對應關係
    ```json
    {
        "id": 1,
        "name": "JJJ",
        "contact_email": 22
    }
    ```

    為了對應前端的 snake case 命名法，可使用此註解指定 key值

    ```java
    @JsonIgnoreProperties(ignoreUnknown = true)
    public class User {
        
        Integer id;
        String name;

        @JsonProperties("contact_email")//若沒有指定key值，此變數會為null
        String contactEmail;
    }
    ```