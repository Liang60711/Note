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


3. Web Server 與 Application Server 的溝通也需要一個傳輸協定，`CGI` (Common Gateway Interface) 是其中一個技術。

<br/>

<br/>

## Apache vs. Tomcat

比喻: 
Apache is a car that can load static objects (HTML static web pages, etc.); but can not be loaded with dynamic water (JSP, CGI, etc.), you need a bucket (container, Tomcat) to load water.

> https://mossgreen.github.io/Servlet-Containers-and-Spring-Framework/