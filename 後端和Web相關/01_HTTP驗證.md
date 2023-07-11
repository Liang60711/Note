## API 驗證方式分為兩種
1. API Key
2. Basic Authentication


<br/>

<br/>

## API Key
* 常用在 Server-to-server 串接 API 時用到，會自定義 Request 的 Header，因為伺服器間都會有白名單，故只要給 api-key，response方就能從資料庫去查找是否有此 api-key 而去做驗證。

    ```sh
    # 通常會以大寫 X 作為自定義 header 的開頭
    X-api-key: admin
    ```

* 不適用在 client-to-server，因為 api-key 在 client 端有暴露的危險，任何人都可以拿此 key 去要資料。例如 google map 的 key 假如放在客戶端，任何人都可以不用付錢的拿此 key 取資料。


* Request 方獲得 api-key 的方式有兩種: 

    1. 內部講好，寫死 api-key。
    2. (常用)在 Response 方產生一組有時效性的 api-key，Response方會將此 api-key 存入該資料庫中，等待後續進行比對。

<br/>

<br/>

## Basic Authentication
* 目的: 可以使用在 client-to-server 及 server-to-server 的驗證模式。
* 使用 HTTP Header 中的 `Authorization` 這個 header，而非 X 開頭的自定義 header。

    ```sh
    # Header 格式
    # Token = base64(username:password)，去除無效字元
    Authorization: Basic Token
    ```
    舉例 `username`: alice, `pwd`: superman
    ```sh
    Authorization: Basic YWxpY2U6c3VwZXJtYW4=
    ```

* challenge機制，常使用在`內部系統`中，有兩種驗證帳號密碼的方式: 

    1. (常用)寫死在環境檔中，每次登入都使用此帳密challenge。

    2. 將帳密存在DB中，challenge時進行比對。

    <br/>
    

<br/>

* 使用情境: 通常使用在內部系統，只需要擋住沒有權限的人，但又不想額外建立一個會員登入系統時，就非常適合使用 basic authentication，與一般會員登入系統的 JWT 和 Session-Cookie 認證方式還是有所區別。


* 缺點: 密碼因為只用 base64 編碼並放在 header 中，代表密碼是暴露在外，相較之下沒這麼安全。

<br/>

<br/>


