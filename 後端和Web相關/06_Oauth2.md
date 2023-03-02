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

## 常見用詞
1. `User (Resource Owner)` 

    能授予應用程式取得受保護的資料的人，通常就是終端使用者，這些人在google或facebook上面有許多資料，故也是 Resource Owner，此角色有權同意或拒絕應用程式取得這些資源。

2. `Client` 

    通常是指想取得受保護資源的`應用程式`。應用程式必須獲得 Resource owner 同意，才能代表使用者去取得相對應的資源。

3. `Authorization Server` 

    驗證 Resource owner 身分，並且在獲得同意後，發放 `Access Token` 給應用程式伺服器(Client)。

4. `Resource Server` 

    存放使用者受保護資訊的伺服器，應用程式(Client)需要帶著 `Access Token`，透過API去取得資料，需要保護資料被未授權的人取得。

5. `Authorization Grant` 

    在流程中，用來證明使用者同意賦予應用程式一定程度的授權、同意應用程式去做某些事、取得一個範圍內的資源。

<br/>

<br/>

## References
> https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015