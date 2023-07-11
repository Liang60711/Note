## Accept 與 content negotiation

1. 屬於 request 的 header。

2. 與`內容協商 (content negotiation)`相關 : 瀏覽器發起的 request，告訴服務器可以接受哪些類型的文件

    ```java
    // */* 代表接收全部內容
    // 權重配置 : `q` 代表權重，優先選擇最合適類型設置 `Content-Type` 返回。
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
    ```

3. Springboot 可依照 request 中的 Accept，自動選擇最適合類型的檔案格式返回，稱為`內容協商`，流程如下:


    1. 判斷當前 response header 中是否已經有確定的媒體類型。
    2. 獲取客戶端支持接收的內容類型(獲取客戶端 Accept header)。
    3. 遍歷循環所有當前系統的 `MessageConverter`，看哪個支持這個物件。
    4. 找到支持的 converter，把此 converter 支持的媒體類型統計出來 (springboot支持10種類型)。
    5. 進行內容協商的最佳匹配媒體類型。
    6. 用 converter 將物件進行轉化。

<br/>

<br/>

## Connection

1. 有鑑於每次HTTP連線時，都會經過 TCP 3次握手，才能傳輸 HTTP，傳輸結束後又要經歷 4次揮手，傳輸消耗巨大，因此產生此 header，讓伺服器不會自動關閉連線。

2. `Connection: keep-alive` 這個 header 在 http 1.1 預設就是會自動帶，也就是說在 1.1 預設是長連線，但不代表這個連線會永久存在，通常會有一個 keep-alive timeout 參數會設置一段時間沒有用後，就自動關閉，即 `Connection: close`。

3. 最新的 Tomcat 容器，預設長連接的時間為20秒。