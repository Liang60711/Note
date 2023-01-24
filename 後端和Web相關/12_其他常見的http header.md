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

