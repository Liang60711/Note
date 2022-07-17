## Tomcat 安裝 (windows)
1. 下載 Tomcat，並解壓縮。
2. 新增環境變數 `CATALINA_HOME` 為 `C:\Program Files\apache-tomcat-8.5.79`

3. 新增環境變數(path)

    ```sh
    %CATALINA_HOME%\bin
    %CATALINA_HOME%\lib
    ```

4. 這時啟動 `startup.bat` 會報錯，因為在啟動 startup.bat 會調用 `setclasspath.bat`。

    > Neither the JAVA_HOME nor the JRE_HOME environment variable is defined
At least one of these environment variable is needed to run this program

5. `setclasspath.bat` 檔案需新增
    ```sh
    set JAVA_HOME=C:\Program Files\Java\jdk-18.0.1
    set JRE_HOME=C:\Program Files\Java\jdk-18.0.1
    ```

6. 有可能在瀏覽器產生錯誤，這是因為 tomcat 目錄沒有被讀寫的許可權，導致檔案不能被編譯到指定的工作目錄中。

    > java.lang.IllegalStateException: No output folder

7. 找到 tomcat 目錄，右鍵—>內容—>安全性—>編輯，找到Users，將`完全控制`選項打勾。

<br/>

<br/>

## 修改 port
`conf/server.xml` 中
```xml
<Connector 
    port="8080" 
    protocol="HTTP/1.1"
    connectionTimeout="20000"
    redirectPort="8443" />
```

<br/>

<br/>


## 訪問靜態資源
* 專案應放在 `webapps` 目錄底下，WEB-INF是外部無法訪問的目錄。
* 訪問靜態資源網址為: `localhost:8080/MyProject/index.html`
```java
└─MyProject
    │  
    ├─WEB-INF        // 外部無法訪問
    │    ├─classes   // 放置.class檔案目錄
    │    │   └─ MyServlet.class // Servlet interface的實作類
    │    ├─lib
    │    └─web.xml   // 配置檔案
    │  
    ├─img       
    └─index.html
```

<br/>

<br/>

## 訪問動態資源
* `WEB-INF` 目錄，可存放動態資源(Servlet的實作類)，但是不能直接訪問，需在 `web.xml` 文件中配置。

```xml
<!-- web-app配置 -->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                        http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
    version="3.1"
    metadata-complete="true">

<!-- servlet -->
<!-- 為在 classes 目錄下的實作的 Servlet 取一個名字 -->
<servlet>
    <servlet-name>my01</servlet-name>
    <servlet-class>MyServlet</servlet-class>
</servlet>

<!-- servlet-mapping  -->
<!-- 對上述Servlet做映射的配置，訪問 /myservlet ，會去執行名為 my01 的 class -->
<servlet-mapping>
    <servlet-name>my01</servlet-name>
    <url-pattern>/myservlet</url-pattern>
</servlet-mapping>

</web-app>
```