## Servlet 
1. 為 Server Applet的 簡稱，是 Server 端的程式，是一種動態網頁技術。
2. 為 JavaEE 規範之一，規範就是 interface。
3. 為 JavaWeb 三大組件之一，分別是 `Servlet程式`、`Filter過濾器`、`Listener監聽器`，當 request 進入 Servlet 容器後，最先碰到的是 Filter。
4. 運行在伺服器上的一個 Java 小程序，可以處理 request 並生成 response。
5. Tomcat 即是 servlet 容器的一種，用來產生 servlet，並控制 servlet 的生命週期，但 Tomcat 不單單只是 servlet 容器，還包括可以啟動 Spring 容器。
6. Tomcat 容器在被啟動後， servlet 是不會被自動載入的，servlet 只會在第一次請求的時候被載入和實例化，所以更新版本後第一次請求會特別慢。
7. Servlet 在容器中被載入後，通常不會被容器刪除，直到容器被重啟、關閉、內存回收時。
8. Servlet 作為 HTTP Request 進入 Java 物件的一個起始點，由 Servlet 容器來管理所有的 Servlet。

<br/>

<br/>

## 傳統 CGI 模式 / Servlet
> https://www.geeksforgeeks.org/difference-between-java-servlet-and-cgi/

CGI為一個規範，可以由任何程式語言實作所產生，當請求打進伺服器時，CGI 會將每一個請求(Request)產生一個程序(Process)，導致效能緩慢。

<img src="https://media.geeksforgeeks.org/wp-content/uploads/20200612201322/CGI2.png">

<br/>

<br/>

為了解決傳統CGI的效能問題，Servlet將請求(Request)細化成執行緒(Thread)，讓部分資源可以共享，使效能提升。

<img src="https://media.geeksforgeeks.org/wp-content/uploads/20200612200618/Servlet2.png">

<br/>

<br/>


## Servlet Container vs. Spring IoC Container

> https://mossgreen.github.io/Servlet-Containers-and-Spring-Framework/

<img src='https://user-images.githubusercontent.com/8748075/86555900-d9095d00-bfa5-11ea-87f9-fac27fc6de3f.png'>

request 順序: 
0. 可能有 Apache server
1. Servlet Container
2. Servlet Filter
3. Servlet
4. Spring Container
5. Spring Security filter chain, FilterProxy(DelegatingFilterProxy)
6. DispatcherServlet(轉發器，也繼承HttpServlet類，但是隸屬在 Spring 容器底下)
7. Interceptor
8. AOP


<br/>

<br/>

## SpringBoot 中的嵌入式 Servlet容器自動配置、啟動原理

> https://blog.csdn.net/weixin_44730681/article/details/107722759

簡單來說就是

1. 入口 main() 方法
啟動 Spring IoC 容器
2. 通過 `createWebServer()` 掃描 IoC 容器中是否有對應的 `ServletWebServerFactory` 工廠類，若有就會進行 Tomcat(預設)屬性配置。
3. 最後創建 `TomcatWebServer` 啟動 Tomcat 容器。

4. 雖然 IoC 容器和 Servlet 容器是兩個不同的管理物件，但皆是由 Springboot 啟動時創建(內嵌)。

<br/>

<br/>

## ServletContext vs. ApplicationContext

> 根據上面那張圖，左邊容器為 Servlet Container，右邊容器為 Spring IoC Container。

`ServletContext`:

1. 當 servlet 容器(如 Apache Tomcat)啟動時，將部署和加載其所有 Web 應用程序。

2. 當加載 Web 應用程序時，servlet 容器會創建一次 ServletContext 並將其保存在服務器的內存中，然後，cotnianer 通過調用它們的 init() 方法初始化並加載所有過濾器、servlet 和偵聽器。

3. 當 servlet 容器完成上述所有初始化步驟時，將調用 ServletContextListener.contextInitialized()。

4. 負責HTTP請求和響應的容器，並負責Servlet的生命週期管理。


`ApplicationContext`: 

1. ApplicationContext 表示 Spring IoC 容器，負責實例化、配置和組裝上述 beans。

2. Spring Boot 是使用 Spring 配置來初始化IoC容器和嵌入式 Servlet 容器。

3. 每個 Application 都會有一個 ApplicationContext。

4. WebApplicationContext 接口是繼承自 ApplicationContext 接口，兩者皆是IOC容器的核心接口。ApplicationContext 通用於 Java 應用程序中，而 WebApplicationContext 專門用於 Web 應用程序。


`總結`: 

在一個Java Web應用程序中，IOC容器和Servlet容器通常是配合使用的。例如，當一個HTTP請求到達Servlet容器時，Servlet容器會創建一個Servlet實例來處理請求，並從IOC容器中獲取所需的依賴，以完成請求的處理，相反，IOC容器也可以取Servlet容器中的物件。