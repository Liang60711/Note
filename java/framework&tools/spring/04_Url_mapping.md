## @RequestMapping
1. 加在`class`或`方法`上，可以url的相對路徑。
2. 相應的class必須加上`@Controller`或`@RestController`，url才會生效。

    ```java
    @RequestMapping("/product") // url prefix
    @RestController
    public class ProductController{
        
        // url = /product/detail
        @RequestMapping("/detail")
        public String detail(){
            return "detail";
        }

        // url = /product/list
        @RequestMapping("/list")
        public String list(){
            return "list";
        }
    }

    ```

3. 當被`@RequestMapping`註解的方法返回類型是自定義類型，則會將`返回的物件`轉換為json格式。

4. Spring Boot使用`Jackson Library`將Java物件轉為json格式。

    >https://github.com/FasterXML/jackson

<br/>

<br/>

## @Controller / @RestController
1. 只能加在class上
2. 用途是讓該class成為bean，並且可以使用`@RequestMapping`。
3. 兩者的差別是`@Controller`需在方法再加上`@ResponseBody`才能回傳Json格式。 

    `@RestController` = `@Controller` + `@ResponseBody`


4. 回傳json給前端

    ```java
    // 使用@RestController
    @RestController
    public class Mycontroller {

        @RequestMapping("/user")
        public Student user(){
            Student student = new Student();
            student.setName("Jeep");
            return student; //@RequestMapping會自動將物件轉為json
        }
    }
    ```
    ```java
    // 使用@Controller
    @Controller
    public class Mycontroller {

        @RequestMapping("/user")
        @ResponseBody   //多寫一行
        public Student user(){
            Student student = new Student();
            student.setName("Jeep");
            return student;
        }
    }
    ```

<br/>

<br/>

## json
json的key只能是`字串`，value可以支援4種基本類型`整數`、`浮點數`、`字串`、`Boolean`，空值`null`。


<br/>

<br/>

## 取資料
有4種方法:
1. `@RequestParam` : 取query parameter
2. `@RequestBody` : 取 request body 內容
3. `@RequestHeader` : 取 request header
4. `@PathVariable` : 取 url 當中的路徑名稱(Restful)

<br/>

<br/>

## @RequestParam (GET)
1. 可以取 url 中的 query parameter，例如:

    `localhost:8080/test?testId=123&name=lil`

    ```java
    @RestController
    public class MyController{
        
        @RequestMapping("/test")
        public String test(@RequestParam String name
                           @RequestParam Integer testId){ // name,testId變數名稱需和query的key名相同
            
            return "";
        }
    }
    ```

2. `@RequestParam`的參數有3個:


    `name`參數(不常用)，可以指派參數的值給變數，例如: 
    
    `localhost:8080/test?testId=123`
    ```java
    // 將testId的參數123指定給id使用
    @RequestMapping("/test")
    public String test(@RequestParam(name = "testId") Integer id){
        ...
    }
    ```
    `required`參數，定義是否為必填，預設為true，若沒收到該參數則返回 `400` 給前端。

    ```java
    @RequestMapping("/test")
    public String test(@RequestParam(required = false) Integer id){
        ...
    }
    ```

    `defaultValue`提供預設值，為`required=false`的加強版，default值需給字串，可和`name`一起用。

    ```java
    @RequestMapping("/test")
    public String test(@RequestParam(defaultValue = "10") Integer id){
        ...
    }
    ```


<br/>

<br/>

## @RequestBody (POST)
1. 抓取Post給後端的Request body中的參數。
2. spring會自動將json格式對應到接收參數的java class中。

    ```json
    // json
    {
        "id":123,
        "name": "Judy",
        "score": 100
    }
    ```

    ```java
    @RestController
    public class MyController{

        @RequestMapping("/test2")
        public String test2(@RequestBody Student student){
            return "";
        }
    }
    ```
    ```java
    // 用Student物件去接前端拋過來的json
    public class Student{
        Integer id;
        String name;
    }
    ```
3. 若前端給的json多帶了key，則java class會`忽略`這筆，如果少帶了key，則會設成`null`。

    ```java
    // 接資料的student class，name會設成null
    {
        "id": 123,
        "score": 100
    }
    ```
    

<br/>

<br/>

## @RequestHeader (取Header)
1. 取 Request header，header的結構一樣是 `key: value`
2. 接收變數的名稱也需要和header的key相同。

    ```java
    @RestController
    public class MyController{

        @RequestMapping("/test3")
        public String test3(@RequestHeader String header1){
            return "";
        }
    }
    ```
3. 參數設定

    `name`，設定變數名稱應對應哪個header key(常用，因為header字串中有 `-` 符號)
    ```java
    @RequestMapping("/test3")
    public String test3(@RequestHeader(name="Content-Type") String contentType){
        ...
    }
    ```
    `required`，設定是否為必須的header
    ```java
    @RequestMapping("/test3")
    public String test3($RequestHeader(required=false) String header1){
        ...
    }
    ```
    `defaultValue`，設定預設值
    ```java
    @RequestMapping("/test3")
    public String test3($RequestHeader(defaultValue="application/json") String header){
        ...
    }
    ```

<br/>

<br/>

## @PathVariable
1. 取在path中的路徑變數，Restful的url會常用到。
2. 使用格式是使用 `{}` 取路徑中的變數。
3. {id} 裡面的參數名稱，必須和方法中的參數名相同。

    ```java
    @RestController
    public class MyController{

        @RequestMapping("/test4/{id}")
        public String test4(@PathVariable Integer id){//變數命名需統一
            System.out.println(id);
            return "test4";
        }
    }
    
    ```

<br/>

<br/>

## IntelliJ
若要查詢此class中，所有 route list，可以點選下方 `endpoint` tab。


<br/>

<br/>