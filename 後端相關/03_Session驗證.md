## Session 
* 目的: 將第一次 client 傳來的資訊儲存在 server 端中，不用將有安全性的資料在 HTTP 中傳輸。


* 流程: 
    1. 使用者登入，server 會將使用者資訊存在 `session store` 中，並生成 `session ID` 作為索引。
    2. 回傳給使用者 Set-cookie 的 header，裡面存有`session ID`。
    3. 使用者再請求時，cookie 會帶上 `session ID`。
    4. server 會比對 `session ID` 和 `session store` 中的資料是否正確。

<br/>

* 缺點: 只要任何人拿到這組 session ID，都可以取代該使用者進行非本意的操作，因此限制 session, cookie 的到期時間是很重要的。


<br/>

<br/>


## Session store
* 存在於伺服器端，用來存放 Session data。
* 存放位置:

    1. 記憶體內存
    2. redis 或 memcached。
    3. 資料庫