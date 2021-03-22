# 瀏覽器到伺服器
1. 瀏覽器(Client)
2. 路由器(Router)
3. 代理伺服器(Proxy):
    * 有些防火牆有訪問限制，所以需要透過有權限訪問的伺服器來訪問目標伺服器。
    * 讓伺服器不會知道真正的 Client 是誰。

4. 網域名稱系統(DNS，Domain Name System)

* 以下為 伺服器端
5. 負載平衡器(Load balance): 
    * 分配 request 給伺服器1, 2, 3...，通常為訪問需求較大的伺服器，才能分流訪問次數。
6. 反向代理伺服器(Reverse Proxy): 
    * 反向代理可讓訪問者不會直接訪問實體伺服器，多一層防護。
    * 若一個伺服器裡面有不同的 application 或其他 web server，所以需要分配 Port 號。
    * 反向代理也可以是負載平衡器


* 上網的訪問順序為 1 >> 2 >> 3 >> 4 >> 5(可能沒有) >> 6 >> Application，並原路返回。




<br>

# cookies 和 session 差別
由於 http 是無狀態的，所以server不知道是誰request，故產生了cookies 和 session。  
  
## cookies 
存在於**客戶端**的瀏覽器當中的文本，其中資料包含了
* value
* expiration time過期時間
* domain: 將含有domain name(一級域名)的網址都可以使用cookies，讓子domain name(二級域名)也可以使用
* path: 與domain類似，需含有path路徑的網址才能使用cookies
* secure: 一個標記，在 https 中，都會加入 secure 選項
* http-only: 只能透過 http 傳輸，避免被 javascripts 跨站腳本攻擊竊取 cookies

## session 
存在於**server端**，當server接受一個request(第一次)時，會產生一個cookies，名為**set_cookies**的文本，由server回傳給客戶端的瀏覽器，其中包含了session_id；客戶端的瀏覽器便會將set_cookies作為cookies，當客戶端發送第二次request時，cookies會一同傳送，其中包含session_id一同以 key: value的方式傳遞，即可讓server認得用戶。

如果客戶端禁止使用cookies，session就只能用 URL 來傳遞參數。


# Cache
Cache: 為了增加處理速度跟效率，server 會將一些 request 的內容儲存在某個地方，通常這地方稱做為 Cache，之後如果有 Request 想要同樣的內容，就直接傳送給它。
（這樣 server 就不用每次都根據 rquest 去 DB 或其它地方收集資訊再 Response）