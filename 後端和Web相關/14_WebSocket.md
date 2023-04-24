## 重點
1. 屬於OSI模型中的應用層，基於傳輸層的TCP協議。
2. 此協議是`瀏覽器`和`伺服器`交換資料的協議。
3. 與 HTTP 最大的不同是，只需連線一次，就能保持雙向溝通，不需要重複發送 Request，提升通訊速度。
4. 此協議允許伺服器向瀏覽器端推播資料，有效解決HTTP中`輪詢(Polling)`產生的效能問題。
5. WebSocket 是建立在 HTTP 協議之上的一種協議，它使用了 HTTP 協議的握手協議來建立連接。在 WebSocket 握手成功之後，客戶端和服務器之間的通信將不再遵循 HTTP 協議的規則，而是採用 WebSocket 自己的數據傳輸協議


<br/>

<br/>

## 實作
websocket 的建立是透過 javascript，發起一個 HTTP request，並包含一個特殊的 header，該 header 表示客戶端希望升級協議到 websocket，如果該伺服器支援 websocket，則在響應時，也會再包含此 header。

```sh
# no ssl
ws://example.com/wsapi
# ssl
wss://example.com/wsapi
```

WebSocket 進行一次雙方握手過程，Client 端發送資訊：

```
GET ws://localhost:8080 HTTP/1.1             // 透過 HTTP/1.1 進行交握，一定要使用GET
Origin: http://localhost:8080                // Client 端 URL
Host: localhost:8080
Upgrade: websocket                           // 表示發送 WebSocket 類型請求
Connection: Upgrade                          // 表示 Client 希望連接升級
Sec-WebSocket-Key: xqBt3ImNzJbYqRINxEFlkg==  // base64 編碼密文，瀏覽器在每次握手隨機生成
Sec-WebSocket-Protocol: chat, superchat      // 用來區分同 URL 下不同 Server 需要之協議
Sec-WebSocket-Version: 13                    // 支持的 WebSocket 版本號
```

<br/>

<br/>

## 參考資料

> https://hackmd.io/@Heidi-Liu/javascript-websocket#11-What-is-WebSocket