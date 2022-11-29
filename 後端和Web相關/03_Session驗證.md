## Session 模型
* 目的: 將第一次 client 傳來的資訊儲存在 server 端中，不用將有安全性的資料在 HTTP 中傳輸，只使用 Session ID 進行驗證。

* 使用情境:
    1. `伺服器渲染html`，與前後端分離不同，由於是伺服器渲染頁面 (SSR，server side rendering)。

* 流程: 
    1. 使用者登入，server 會將使用者資訊存在 `session store` 中，並生成 `Session ID` 作為索引。
    2. 回傳給使用者 Set-cookie 的 header，裡面存有`Session ID`。
    3. 使用者再請求時，cookie 會帶上 `Session ID`。
    4. server 會比對 `Session ID` 和 `session store` 中的資料是否正確。

<br/>

* 缺點: 
    
    1. 只要任何人拿到這組 Session ID，都可以取代該使用者進行非本意的操作，因此限制 session, cookie 的到期時間是很重要的。
    2. 由於每次客戶端每次請求時，伺服器都要拿此 Session ID 跟 Session store 做比對，造成伺服器端壓力過大，雖說 redis 可以很稱職地擔任 Session store，但總有極限。
    3. 此模型是以 Session store (redis) 中心化管理 Session ID，故 redis 只能垂直擴充。


<br/>

<br/>


## Session store
* 存在於伺服器端，用來存放 Session data。
* 存放位置:

    1. 記憶體內存
    2. redis 或 memcached。
    3. 資料庫