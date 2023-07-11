## 幾個重點
1. RestTemplate 是 Spring3 開始的類，新版的 WebClient 是 Spring5 新加入的類，如果要寫新版本，`建議使用 WebClient`。
2. RestTemplate 和 WebClient 差異: 

    WebClient 是基於 Reactive Streams 的非阻塞式 HTTP 客戶端，支持異步請求和響應處理。它使用 Java 8 的 CompletableFuture 或者 Reactor 的 Mono 和 Flux 來處理異步操作。相比之下，RestTemplate 是基於傳統的 Servlet API 的同步阻塞式客戶端，它在發送請求時會一直等待響應返回，所以使用 RestTemplate，必須要設定 Timeout，以免阻塞。
3. RestTemplate 的底層是 HttpClient。

<br/>

<br/>

## RestTemplate 發起 HTTP request / 打 API
`RestTemplate` : GET 請求外部 API

<br/>

`getForObject()` : GET 後，返回 json 並轉為指定類別

`getForEntity()` : GET 後，返回 ResponseEntity 物件
```java
@RestController
public class RestTemplateController {

    /**
     * getForObject 
     */
    public String getForObject() {
        RestTemplate restTemplate = new RestTemplate();

        String url = "https://mocki.io/v1/9ba6f589-d9ad-49ca-9a75-0be8710404fd";
        Map<String, Object> queryParamMap = new HashMap<>();
        queryParamMap.put("key", "value");

        // 使用 getForObject 發起 get 請求
        // getForObject(url, responseType, queryParams)
        Student student = restTemplate.getForObject(
                url,
                Student.class,
                queryParamMap);

        return student.toString();
    }

    /**
     * getForEntity 
     */
    public String getForEntity() {
        RestTemplate restTemplate = new RestTemplate();

        String url = "https://mocki.io/v1/9ba6f589-d9ad-49ca-9a75-0be8710404fd";
        Map<String, Object> queryParamMap = new HashMap<>();
        queryParamMap.put("key", "value");

        // 返回整包 HTTP response(status code, header, body)
        ResponseEntity<Student> studentEntity = restTemplate.getForEntity(
            url,
            Student.class,
            queryParamMap);

        // 可以再去取物件
        Student student = studentEntity.getBody();

        return "";
    }
}
```
```java
// 同樣可以使用 @JsonProperty 來指定 json 的 key
public class Student {

    Integer id;

    String name;

    @JsonProperty("contact_phone")
    String contactPhone;

}
```

<br/>

<br/>

## 序列化(物件轉json)
預設轉json時，key 命名方式都是 camel case，可以更換為 snake case
```java
@Data
@JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy.class)
public class Student {
    private Ling id;
    private String name;
}
```


<br/>

<br/>

`postForObject()` : POST 後，返回 json 並轉為指定類別

`postForEntity()` : POST 後，返回 ResponseEntity 物件
```java
@RestController
public class RestTemplateController {

    /**
     * postForObject 
     */
    public String postForObject() {
        RestTemplate restTemplate = new RestTemplate();

        String url = "https://mocki.io/v1/9ba6f589-d9ad-49ca-9a75-0be8710404fd";

        Student requestObject = new Student();
        requestObject.setName("JJJ");

        // 第二個參數為要 post 的 java 物件
        // 第三個參數為返回物件的類型
        Student student = restTemplate.postForObject(
                url,
                requestObject,
                Student.class);

        return student.toString();
    }
}
```

`exchange()` : 可發起GET或POST請求

```java
// GET
public String exchange1() {
    // 1. 建立 restTemplate
    RestTemplate restTemplate = new RestTemplate();

    // 2. 建立要發起的 requestEntity
    HttpHeaders requestHeaders = new HttpHeaders();
    requestHeaders.set("header1", "value1");
    HttpEntity requestEntity = new HttpEntity(requestHeaders);

    // 3. 建立 query params
    Map<String, Object> queryParamMap = new HashMap<>();
    queryParamMap.put("key", "value");

    // 4. 呼叫 exchange()
    ResponseEntity<Student> getStudentEntity = restTemplate.exchange(
            "http://localhost:8080/test",
            HttpMethod.GET,
            requestEntity,
            Student.class,//返回物件
            queryParamMap
    );

    return "";
}

```

```java
// POST
public String exchange2() {
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders requestHeader = new HttpHeaders();
    // POST一定要加這行header
    requestHeader.setContentType(MediaType.APPLICATION_JSON);

    Student studentRequestBody = new Student();
    studentRequestBody.setName("JJJ");

    HttpEntity requestEntity = new HttpEntity(studentRequestBody, requestHeader);

    ResponseEntity<Student> postResult = restTemplate.exchange(
        "http://localhost:8080/test",
        HttpMethod.POST,
        requestEntity,
        Student.class
    );

    return "";
}
``

<br/>


<br/>

## 直接取 raw data 字串
```java
String url  = "https://api.coindesk.com/v1/bpi/currentprice.json";
// 使用 String.class 取字串
final String sss = restTemplate.getForObject(url, String.class);
System.out.println(sss);
```