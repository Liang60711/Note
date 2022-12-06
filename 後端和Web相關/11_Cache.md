## Http Cache 快取

> https://pjchender.dev/webdev/note-http-cache/

在 Http Request 和 Response 的 Header 中，有一個 header 叫做 `Cache-Control`。

有以下四種值:
1. `Cache-Control: max-age=...` : 瀏覽器自行判斷，有機會讓瀏覽器完全不送出請求。
2. `Last-Modified` 和 `eTag` : 會在瀏覽器產生對應的 `If-Modified-Since` 和 `If-None-Match` 標頭，伺服器在根據此標頭判斷是否要回傳新的檔案，或回傳 304 Not Modified 即可。
3. `Cache-Control: no-store` : 瀏覽器將不會快取任何內容（永遠不要快取）。
4. `Cache-Control: no-cache` : 瀏覽器會快取所有內容，但每次都會發送請求向伺服器詢問是否有新內容要提供（永遠檢查快取）。