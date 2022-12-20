## Http Cache 快取

> https://pjchender.dev/webdev/note-http-cache/

在 Http Request 和 Response 的 Header 中，有一個 header 叫做 `Cache-Control`。

有以下四種值:
1. `Cache-Control: max-age=...` : 瀏覽器自行判斷，有機會讓瀏覽器完全不送出請求。
2. `Last-Modified` 和 `eTag` : 會在瀏覽器產生對應的 `If-Modified-Since` 和 `If-None-Match` 標頭，伺服器在根據此標頭判斷是否要回傳新的檔案，或回傳 304 Not Modified 即可。
3. `Cache-Control: no-store` : 瀏覽器將不會快取任何內容（永遠不要快取）。
4. `Cache-Control: no-cache` : 瀏覽器會快取所有內容，但每次都會發送請求向伺服器詢問是否有新內容要提供（永遠檢查快取）。

<br/>

<br/>

## header 作用
`X-Content-Type-Options` : 瀏覽器會自動猜每個資源的檔案類型(Content-Type)，例如: "text/javascript"，但有資安問題，攻擊者可以上傳含有JS程式碼的JPG檔案，並將圖片被載入到前端導致惡意腳本被執行，造成XSS攻擊。

`Content-Type-Options: nosniff` 表示防止瀏覽器亂猜造成資安問題，表示瀏覽器直接使用 header 中的 Content-Type，就不會有純文字、圖片被判斷成腳本，但缺點就是各種資源的 Content-Type 都需要設定好。