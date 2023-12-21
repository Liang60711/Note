## JOSE (JSON Object Signing and Encryption) 標準
* JWA - Json Web Algorithms
* JWE - Json Web Encryption
* JWK - Json Web Key
* JWS - Json Web Signature

<br/>

<br/>

## JWT 模型
* 使用情境:

    1. `授權`，client-to-server 登入，使用者再次對 Server 端發送請求的時候，會夾帶著 JWT，允許使用者存取該 token 有權限的資源。
    2. `訊息交換`，JWT 可以透過公鑰/私鑰來做簽章，可以知道是誰發送這個 JWT，此外，由於簽章是使用 header 和 payload 計算的，因此還可以驗證內容是否遭到篡改。

    3. `前後端分離`，前端可以透過 JWT 方式跟後端請求資料，由於前端屬於靜態頁面，搭配 JWT 靜態特性很搭。

* 流程: 

    1. 伺服器端在收到登入請求後驗證使用者。

    2. 伺服器端產生和回傳一組帶有資訊，且僅能在伺服器端被驗證的 Token。

    3. Token 被回傳後，儲存在「客戶端」，大多存在瀏覽器的 Storage 當中，也可以`將 Token 放在 Cookie(HttpOnly=True)`，這樣可以避免 Javascript 進行存取，但以上這兩種方式都不是最安全的。
    
    4. 往後客戶端向伺服器端發送請求時，皆附帶此 Token 讓伺服器端驗證。

    5. 若伺服器端在請求中沒有找到 Token，回傳錯誤；若有找到 Token 則驗證通過。

* 優點: 

    1. 由於 JWT 中可以攜帶 payload 資料，不需要每次請求都去讀取資料庫使用者資料，減少伺服器負擔。

    2. 無狀態的驗證，不需要在資料庫儲存用戶資訊，只需要一組 secret (或稱為 private key)，就可以驗證資料的正確性。

* 缺點:

    1. Token被放在客戶端，有安全性問題。

<br/>

* HTTP Header 格式: 與 Basic authentication 相同，是使用 Header 進行驗證。

    ```sh
    # HTTP Header 格式
    Authorization: Bearer Token
    ```

<br/>

<br/>


##  JWT 組成


<br/>


* JWT 是一組字串，透過 `.` 符號切分成三個為 Base64 編碼的部分。

    ```sh
    # 格式，每段必須用 base64 編碼
    base64(Header) + base64(Payload) + base64(Signature)
    # 舉例
    xxxxx.yyyyy.zzzzz
    ```

1. `Header` : 紀錄這個 token 是用何種演算法去產生出來的。

    ```json
    {
        "alg": "HS256", 
        "typ": "JWT"
    }
    ```
    

2. `Payload` : 紀錄資料，使用者透過此資料可以聲稱(claims)這筆資料屬於誰，`不可以放機密資訊`。

    ```json
    {
        "iss": "JWT",            // 簽發者
        "iat": 1234567899,       // 簽發時間
        "exp": 9876543211,       // 過期時間
        "aud": "www.example.com",// 接收方
        "sub": "JWT@apple.com"   // 用戶
    }
    ```

3. `Signature` : 用來定義前上述兩個資料的正確性，由於 `secret` 是在 server 端儲存，可以確保資料不會被竄改，HS256為雜湊演算法。

    ```
    signature = HS256(base64(header) + base64(payload) + secret) 
    ```
    

* 使用 JWT 的重點不在於把資料隱藏起來，畢竟 payload 資料是可以被轉換回來的
但由於伺服器端才擁有密鑰，因此即使 payload 被修改，轉換成 Base64 重新置入 Token 中，透過與 Signature 比對之下，就能發現資料的不一致，產生驗證錯誤。


<br/>

<br/>

## 現今開發的model - 整合 Session-Cookie 模型和 JWT 模型
* JWT不夠安全的原因 : 由於 Token 是存在客戶端的 Local storage 或是 HttpOnly Cookie 中，還是有可能被拿走 (只要存在客戶端都是不安全的)。


* 名詞解釋:

    1. `refresh-token` : 用來獲得 `access-token`，與 Session ID 產生的方法類似，即產生一組 uuid，存在客戶端的 Cookie，到期時間較長。
    2. `access-token` : 或稱 `auth-token`，就是 JWT 的 token，用來向伺服器獲取授權資料，會使用 In-Memory 方式或存在客戶端瀏覽器中(LocalStorage)，到期時間較短。


* 流程

    1. 客戶端第一次登入伺服器。
    2. 伺服器產生一組 refresh-token，存在 Session store 中，並將 refresh-token(放在 HttpOnly Cookie) 和 access-token(JWT token)，一起回傳給客戶端。
    3. `查詢資料` : 客戶端訪問伺服器時，Request Header 帶上 access-token，驗證 JWT 簽章通過後，獲取私人資料。
    4. `換發access-token` : 客戶端訪問伺服器時，Request Cookie 帶上 refresh-token，於 Session store 中查詢是否在效期內且未註銷，如果通過驗證則將舊的 refresh-token 註銷，並產生新的 refresh-token 和 access-token 給客戶端。
    5. `客戶端登出時` : 伺服器會從 Request Cookie 中取得 refresh-token，並從 Session store 中註銷該用戶的 refresh-token。

<br/>

<br/>

> 實作參考: https://dotblogs.com.tw/wasichris/2020/10/25/223728