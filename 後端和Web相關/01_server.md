## Web Server / Application Server
> https://vicxu.medium.com/web-server-and-application-server-5a6d9c940eff

1. `Web Server` : 
    * 分為靜態Web Server 和 動態Web Server: 
        
        1. 靜態的主要只能回傳靜態資料，如 apache、nginx，主要關注在 HTTP 協定的傳輸。

        2. 動態Web Server 包含了靜態Web Server、Application Server、DB，可以依照不同請求，回應不同的資料，如 Tomcat，是一個帶有 Servlet Container 的動態 Web Server，可以獨立方式、嵌入方式運作。

    * 補充: 靜態Web Server 可以做為 Application Server 的代理接受 Client 的請求，並將請求傳給 Application Server，請他執行程式後並回傳結果給 Web Server，最後 Web Server 透過 HTTP 回傳給 Client。

2. `Application Server` : 
    * 負責管理並執行業務邏輯以及資料庫的存取
    * 無法透過 HTTP 協定直接與 client 溝通，他只能接受從 Web Server 傳過來的 request 並回傳執行後的結果。


3. Web Server 與 Application Server 的溝通也需要一個傳輸協定，`CGI` (Common Gateway Interface) 是其中一個技術，CGI 在早期的 Web 開發中得到廣泛應用，但由於它的執行方式可能導致性能瓶頸，現代的 Web 開發中更常使用其他技術，如FastCGI、Java Servlets、ASP.NET 等，來實現類似的功能並提高效能。

<br/>

<br/>

## Apache vs. Tomcat

比喻: 
Apache is a car that can load static objects (HTML static web pages, etc.); but can not be loaded with dynamic water (JSP, CGI, etc.), you need a bucket (container, Tomcat) to load water.

> https://mossgreen.github.io/Servlet-Containers-and-Spring-Framework/

<br/>

<br/>

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


* 一個request的順序為 1 >> 2 >> 3 >> 4 >> 5(可能沒有) >> 6 >> Application，並原路返回。



<br>

## cookies 和 session 差別
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

<br/>

# Http methods
### 共有以下 8 種方法，方法基本上都是大寫，前5種比較常見
* GET：查詢 (Read)。
* POST：新增 (Create)。
* PUT：完整更新 (Update/Replace)。
* PATCH：部分更新 (Partial Update/Modify)。
* DELETE：刪除 (Delte)。
* HEAD：同GET，但 response 中不回傳主體 (response body)，用來測試連線或更新cache。
* TRACE：迴路返回測試。
* OPTIONS：跨來源資源共用 (CORS)。

<br/>

### 8種方法，分為 safe、idempotent 兩類

**Safe Methods**:  
client 的請求僅限於讀取 (read-only)，且不改變 server 的狀態，此方法則為Safe Methods。

**Idempotent Methods**:  
client 發出一次或多次同樣的請求對 server 以及資源狀態(資料庫中的資料)的影響結果不變，此方法為 Idempotent Methods。就算同樣的請求即使不小心送了兩次，也不用擔心對 server 造成影響。目的是請求失敗可以自動重送而不用擔心造成不同的結果。

<br/>

| Method  | Safe | Idempotent |
|--|:--:|:--:|
| GET     | yes | yes |
| POST    | no  | no  |
| PUT     | no  | yes |
| PATCH   | no  | no  |
| DELETE  | no  | yes |
| HEAD    | yes | yes |
| CONNECT | no  | no  |
| TRACE   | yes | yes |
| OPTIONS | yes | yes |

<br/>

### PUT 和 PATCH 差別
* PUT 是完整更新資料，將舊資料全部取出並更新。若要修改會員的電話號碼，會先取出原有的會員資料，修改了電話號碼然後送出請求更新。因此請求不管一次或多次結果都相同，屬於 Idempotent Methods。
* PATCH (義同:補丁) 與 PUT 不同在於 PATCH 不會將舊資料全部取出修改後再送回，而僅送出要修改部分的參數。
* 以修改會員資料為例，使用 PUT 會把會員資料重頭到尾再填一次後更新；PATCH 只需要修改需要更新的欄位。


### Safe methods 的例外
* 基本上 GET 不會去改變資源狀態，但**觀看人數**會被 GET 所改變，因此為例外。
* 通常不用太鑽牛角尖去限制 GET 一定要符合 Safe Methods，實務上不會太去在意。


<br/>

<br/>


## Client to Server 模式
Client 透過某些方法和 Server (最終要登入的網站 Server) 驗證，稱為 **Client to Server 模式**。

舉例，常見的第三方登入，步驟如下: 
1. Client 輸入需要的資料給第三方網站 (Google、FB) 做驗證。
2. 驗證通過後，第三方網站將一串 token 丟給 Server。
3. Server 有了 token 之後，就可以和第三方網站請求 Client 資料。

<br/>

## Server to Server 模式
在經過**Client to Server 模式**驗證成功後，Server 可能也會使用到另外 Server 的服務 (例如: 購物網站 Server 會用到金流 Server 的服務)。

此模式會比 **Client to Server 模式** 驗證簡易許多，因為 Server 之間都在防火牆內，或是 Server 有建立白名單。

<br/>

## Authentication / Authorization 差異
* Authentication 驗證: 證明使用者是否真的是其所宣稱的那個人的過程 (是否可以做)。
* Authorization 授權: 授與已驗證的合作對象權限以執行某些工作的動作 (可以做到什麼程度)。

* 已被 Authorization 的使用者，必通過 Authentication。


<br/>

<br/>

# Token
意思大致為通行證、驗證碼，分為以下3類: 
* Event-based: 因事件而觸發，而且通常為一次性的
，稱為 <code>OTP</code> (one-time password)，舉例: 轉帳時，會有簡訊發送一次性密碼。

* Time-based: 具有時效性的驗證碼，一般稱的 <code>Token</code> 歸為此類。舉例: 修改密碼時會觸發寄送 email，並且該連結會有倒數計時。

* static: 即為一般的密碼 <code>Password</code>。