## Oauth2.0

OAuth2.0 依照需求及安全性，大致分為幾種流程

* Authorization Code (Grant Types) 流程，最常見:

    * 適合使用在 Server-side Application，因為要 Access Token 都在後端進行，比較安全。

    1. 由 Application 導向第三方登入連結，此連結會前往登入頁面，此連結的 Query String 通常會帶幾種參數。

        * response_type : `response_type=code` 表示使用 Authorization Code Grant Flow 流程
        * client_id : 在授權伺服器登記的id
        * redirect_uri : 導回的uri
        * scope : 表示請求授權的範圍
        * state : 防csrf

        ```
        https://accounts.google.com/?
            response_type=code&
            client_id=yyds&
            redirect_uri=xxx&
            scope=email,profile&
            state=w6e5r4w65er4w6e5
        ```
    
    2. 當從授權伺服器成功登入後，會詢問使用者是否允許 Application 向授權伺服器取得個人信息，即 scope 參數中的範圍。
    
    3. 當使用者允許後，會導回 redirect_uri (GET method)，並在 query param 帶上 Authorization Code Grant (Grant code 會被前端經手且用 query param 傳送，所以後端必須再使用 Grant code 向授權伺服器要 Access Token，確保安全性)。
    
    4. 因此 Application 就可以使用 POST 方式並帶上Body中的 Authorization Code Grant, client_id, client_secret, redirect_uri, state，給授權伺服器。

    5. 授權伺服器會返回 Access Token，而 Application 就可以使用此 Token 去存取第三方登入平台所提供的的相關資訊 (會有 scope 限制存取範圍)。


* 其他3種流程，可見參考連結。

<br/>

<br/>

## References
> https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015