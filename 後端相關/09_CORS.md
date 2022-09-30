## 同源政策 (Same-Origin Policy)
> reference: https://shubo.io/what-is-cors/
* 同源定義: 
    1. 使用相同協議(http, https)
    2. 使用相同 domain name
    3. 使用相同 port 

<br/>
    
* 同源政策: 

    在同源政策下，非同源的 request 則會因為安全性的考量受到限制。瀏覽器會強制你遵守 CORS (Cross-Origin Resource Sharing，跨域資源存取) 的規範，否則瀏覽器會讓 request 失敗。

<br/>

<br/>

## CORS (Cross-Origin Resource Sharing)
是針對不同源的請求而定的規範，透過 JavaScript 存取非同源資源時，server 方必須明確告知瀏覽器允許何種請求，只有 server 允許的請求能夠被瀏覽器實際發送，否則會失敗。

在 CORS 的規範裡面，跨來源請求有分兩種：
* 簡單請求
    1. 只能是 HTTP GET, POST or HEAD 方法
    2. 自訂的 request header 只能是 `Accept`、`Accept-Language`、`Content-Language` 或 `Content-Type`（值只能是 application/x-www-form-urlencoded、multipart/form-data 或 text/plain）

    <br/>

* 預檢(preflighted)請求。
    * 不符合簡單請求都歸類為預檢請求。
    * 請求會先以 HTTP 的 `OPTIONS` 方法送出請求到另一個網域，確認後續實際（actual）請求是否可安全送出，由於跨站請求可能會攜帶使用者資料，所以要先進行預檢請求。

        <img src="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/preflight_correct.png" width=80%>


* CORS設定(spring boot為例)

    ```java
    // 允許的 header
    private static final String ALLOWED_HEADERS = "x-requested-with, authorization, Content-Type, Authorization, credential, X-XSRF-TOKEN";
    // 允許的 http method
    private static final String ALLOWED_METHODS = "GET, PUT, POST, DELETE, OPTIONS";
    // 允許的網域
    private static final String ALLOWED_ORIGIN = "*";
    // 告訴瀏覽器幾秒之內不用再次預檢
    private static final String MAX_AGE = "3600";
    ```

<br/>

<br/>

