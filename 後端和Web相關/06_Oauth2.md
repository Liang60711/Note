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

    * 補充: 為何第4步不直接發 access token，而需要到第5步才拿到 access token? 
    
        考量的是「安全性」問題，原因是第4步是授權伺服器轉導到 Application 前端，經過前端不安全，故使用一個 grand code，由第5步中的 Application 向授權伺服器要 access token (後端對後端)。


<br/>


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

## OAuth2.0 4種類型
1. Authorization Code Grant

    上述的類型，最為常見，`Server-side Application` 後端渲染的應用程式最為適用。

    資源擁有者、授權服務器、應用程式，三者互相不信任，有不斷確認的流程。

2. Implicit Grant

    適合 `Client-side applications` 的類型，通常會是整個應用程式都在前端運行，依需求向後端 API 取得資料。如單純的靜態網站或 Single Page Application(SPA) 都適用。

    由於整個應用程式都在前端運行，所以會缺少「後端伺服器透過 Authorization Code Grant 交換 Access Token 」的步驟。取而代之的是請 Authorization Server 直接核發 Access Token。

    所以相對於 Authorization Code Flow，沒有這麼安全；換句話說，應用程式和授權伺服器是信任的，可以直接在前端傳遞Token。

3. Resource Owner Password Credentials Grant

    此流程是由使用者提供帳號與密碼等資訊給應用程式，由應用程式直接向 Authorization Server 交換 Access Token，和以往的帳號密碼登入雷同。

    帳號與密碼等機密資訊（credentials）可能會被應用程式儲存起來，作為往後交換 Access Token 使用。因此「必須是使用者高度信賴的應用程式」才適合使用，且唯有前兩種皆不可行時，才會考慮使用當前類型的流程。因此，適用的情境，可能像公司內部的系統。

4. Client Credentials Grant

    通常是由應用程式向 Authourization Server 請求取得 Access Token 以獲取「自己」的相關資源，而非使用者的資源，有點類似 Basic 授權。

    這個流程已經跳脫使用者，因此，使用者身份驗證的流程將不再需要。取而代之的，是應用程式必須向 Authorization Server 提供驗證所需的自身資訊。

<br/>

<br/>

## References
> https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015