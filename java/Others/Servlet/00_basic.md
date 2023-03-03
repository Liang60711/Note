## Servlet 
1. 為 Server Applet的 簡稱，是 Server 端的程式，是一種動態網頁技術。
2. 為 JavaEE 規範之一，規範就是 interface。
3. 為 JavaWeb 三大組件之一，分別是 `Servlet程式`、`Filter過濾器`、`Listener監聽器`。
4. 運行在伺服器上的一個 Java 小程序，可以接收客戶端發送過來的請求，並response數據給客戶端。
5. Tomcat 即是 servlet 容器的一種，用來產生 servlet，並控制 servlet 的生命週期，但 Tomcat 不單單只是 servlet 容器，還包括可以啟動 Spring 容器。
6. Tomcat 容器在被啟動後， servlet 是不會被自動載入的，servlet 只會在第一次請求的時候被載入和實例化，所以更新版本後第一次請求會特別慢。
7. servlet 在容器中被載入後，通常不會被容器刪除，直到容器被重啟、關閉、內存回收時。
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


<br/>

<br/>

## ServletContext vs. ApplicationContext

`ServletContext`:

When the servlet container (like Apache Tomcat) starts up, it will deploy and load all its web applications.

When a web application is loaded, the servlet container creates the ServletContext once and keeps it in the server’s memory.

Then, the cotnianer initializes and loads all filters, servlets and listeners by calling their init() method.

When the servlet container is finished with all of the above described initialization steps, then the ServletContextListener#contextInitialized() will be invoked.


`ApplicationContext`: 

ApplicationContext represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans.

Spring Boot follows a different initialization sequence. Rather than hooking into the lifecycle of the Servlet container, Spring Boot uses Spring configuration to bootstrap itself and the embedded Servlet container. Filter and Servlet declarations are detected in Spring configuration and registered with the Servlet container. For more details, see the Spring Boot documentation.