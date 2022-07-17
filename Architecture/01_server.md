## Web Server / Application Server
> https://vicxu.medium.com/web-server-and-application-server-5a6d9c940eff

1. `Web Server` : 
    * 負責處理 HTTP 協定的傳輸以及接受 HTTP request 與回傳 HTTP response，但只能傳送靜態資料。
    * 可以做為 Application Server 的代理接受 Client 的請求，並將請求傳給 Application Server，請他執行程式後並回傳結果給 Web Server，最後 Web Server 透過 HTTP 回傳給 Client。

2. `Application Server` : 
    * 負責管理並執行業務邏輯以及資料庫的存取
    * 無法透過 HTTP 協定直接與 client 溝通，他只能接受從 Web Server 傳過來的 request 並回傳執行後的結果。


3. Web Server 與 Application Server 的溝通也需要一個傳輸協定，`CGI` (Common Gateway Interface) 是其中一個技術。