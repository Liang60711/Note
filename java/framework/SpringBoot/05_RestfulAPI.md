## HTTP protocol
* Request 需包含4部分
    
    1. HTTP method
    2. url
    3. request header
    4. request body

* Response 需回傳3部分

    1. HTTP status code
    2. response header
    3. response body

<br/>

<br/>

## Status code
`200 OK` : 請求成功。

`201 Created`: 請求成功且新的資源成功被建立，通常用在POST的response。  

`202 Accepted`: 請求已接受，但尚未處理完成。

`204 No Content`: 請求已將目標刪除，目標已不存在。

`301 Moved Permanently`: 永久性重新導向。  

`302 Found`: 臨時重新導向。

`304 Not Modified`: 此 Response 只有 header，沒有body(客戶端收到304後，會從緩存中讀取對應的資源，節省 body 傳輸流量)。

`400 Bad Request`: 前端請求的參數有誤。

`401 Unauthorized`: authorization失敗。

`403 Forbidden`: authentication失敗。

`404 Not Found`: 網頁不存在，可能是url錯誤或資源不見。

`405 Method Not Allowed` : 此 HTTP verb 不支援。

`500 Internal Server Error`: 後端執行程式錯誤，可能是有bug。

`503 Service Unavailable`: 臨時維護或流量太大，後端目前沒辦法處理請求。

`504 Gateway Timeout`: 請求超時。

<br/>

<br/>

## ResponseEntity類 / 自定義方法的 http response
`ResponseEntity<?>` : 自定義`每個方法`所返回的 http response
```java
// 自定義方法的狀態碼和回傳body
@RequestMapping("/test")
public ResponseEntity<String> test(){
    return ResponseEntity.status(HttpStatus.ACCEPTED).body("Hello");//body內的類型，必須為ResponseEntity泛型的類型
}
```
```java
// 原本的寫法
@RequestMapping("/test")
public String test(){
    return "Hello";
}
```

<br/>

<br/>

## 自定義 Exception 的 http response
1. 目的: 將Exception程式統一管理，不用在每個Controller分別寫try-catch。
2. `@ControllerAdvice` 底層是透過 Spring AOP 所實作。
3. 假如拋出錯誤時，沒有對應的 handle 方法，Spring 就會自動去找該 Exception 繼承的父類 (IllegalArgumentException 沒自定義就會去找 RuntimeException)。


4. spring boot在沒有自定義 exception 時，預設給前端的狀態碼為`500`。

    ```java
    @RequestMapping("/test1")
    public String test1(){
        //預設返回500狀態碼給前端
        throw new RuntimeException("test1 error");
    }
    ```


5. `@ControllerAdvice + @ExceptionHandler` : 自定義不同 Exception 所返回的 http response。


6. 實作: 在 MyController 拋出的 RuntimeException，會被自定義的 MyExceptionHandler 接住，並執行 handle() 方法(名字任意)給前端。

    ```java
    @RestController
    public class MyController {

        @RequestMapping("/test1")
        public String test1(){
            throw new RuntimeException("test1 error");
        }
    }
    ```

    `@ControllerAdvice` 會將class變成一個bean，並允許使用`@ExceptionHandler`。  
    `@ExceptionHandler` 可以綁定拋出錯誤時的執行方法。
    
    ```java
    @ControllerAdvice
    public class MyExceptionHandler {

        // return 503
        @ExceptionHandler(RuntimeException.class)
        public ResponseEntity<String> handle(RuntimeException e){//參數e的類型必須和@ExceptionHandler()裡面的參數類型相同
            return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE)
                    .body("RuntimeException: " + e.getMessage());
        }

        // return 400
        @ExceptionHandler(IllegalArgumentException.class)
        public ResponseEntity<String> handle(IllegalArgumentException e){
            return ResponseEntity.status(HttpStatus.BAD_REQUEST)
                    .body("IllegalArgumentException: " + e.getMessage());
        }
    }
    ```

<br/>

<br/>

## Restful 重點
1. 使用 HTTP method 表示動作。
2. 使用 url 路徑描述資源之間的階層關係 (路徑是有階層性的)。
3. response body 需返回 json 或 xml 格式 (@RestController可以不用再加@ResponseBody)。

<br/>

<br/>

## @GetMapping / @PostMapping / @PutMapping / @DeleteMapping
```java
@RestController
public class StudentContoller {

    /**
     * POST， Post有以下2種寫法，推薦第1種
     */
    @PostMapping("/students")
    @RequestMapping(value = "/students", method = RequestMethod.POST)
    public String create(@RequestBody Student student){
        return "create";
    }

    /**
     * GET
     */
    @GetMapping("/students/{sid}")
    public String read(@PathVariable Integer sid){
        return "read";
    }

    /**
     * PUT，需要更改的sid放在url，更新的值放在body
     */
    @PutMapping("/students/{sid}")
    public String update(@PathVariable Integer sid,
                         @RequestBody Student student){
        return "update";
    }

    /**
     * DELETE
     */
    @DeleteMapping("/students/{sid}")
    public String delete(@PathVariable Integer sid){
        return "delete";
    }
}
```