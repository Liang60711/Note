## Http Cache 快取

快取的設定是由 HTTP response header 進行控制。

> https://blog.camel2243.com/2018/09/30/http-last-modified-if-modified-since-etag-%E5%92%8C-if-none-match-%E4%BB%8B%E7%B4%B9/

有以下幾種 header:
1. `Cache-Control`:

    * 有以下四種狀態:

        1. `public` : 公開的資源，可以被所有節點(中繼設備)暫存，如 CDN, ISP Cache，以及最終的目標使用者瀏覽器存取。
        2. `private` : 私有的資源，只被本地使用者的瀏覽器存取。
        3. `no-cache` : 瀏覽器會快取所有內容，但每次都會發送請求向伺服器詢問是否有新內容要提供（永遠檢查快取），使用 `eTag` 確認是否更新。
        4. `no-store` : 瀏覽器將不會快取任何內容（永遠不要快取）。
    
    * public 及 private 可以設定 max-age=... 來指定快取多久後會過期；例如 Cache-Control: private max-age=2592000，描述的是此資源僅可以被本地快取，有效期限是 30 天（60 x 60 x 24 x 30 = 2592000）。


2. `Last-Modified` 和 `ETag` : 
    * 此兩個是 response方的 header。
    * 當 Response header 回傳 `Cache-Control: max-age=86400, no-cache,  Last-Modified: Sun, 01 Jan 2023 00:00:00 GMT`，所以下次瀏覽器需要再次請求時，會帶上 `If-Modified-Since: Sun, 01 Jan 2023 00:00:00 GMT`，讓伺服器去檢查這個時間之後，有沒有更新版本，若有，則回傳 200 和資源並且更新緩存；若沒有，則回傳 304，這樣一來就可以節省 body 的流量。 
    * `ETag` 的使用情境與 `Last-Modified` 相同，只是差別在於 `ETag` 是使用該緩存檔案的 hash code 去檢查有沒有更新，例如 `Cache-Control: max-age=86400, no-cache, ETag: "33a64ef551425fcc55e4d42a148795d9f25f89d4"`。

3. `If-Modified-Since` 和 `If-None-Match` : 
    * 此兩個是 request方的 header。
    * 當客戶端緩存了資源但不确定該緩存資源是否是最新版本的時候，就會發送一個條件請求，會在瀏覽器產生此 header，`If-Modified-Since` 的值為上次response中的 `Last-Modified`；`If-None-Match`的值為上次response中的 `ETag`，伺服器在根據此標頭判斷是否要回傳新的檔案(回傳狀態碼200)，或回傳 304 Not Modified 即可。

4. 結論 : 
    
    * `Last-Modified` 和 `If-Modified-Since`，前者是 response header，後者是 request header，檢查是否更新以日期判斷。
    * `ETag` 和 `If-None-Match`，前者是 response header，後者是 request header，檢查是否更新以 hash code 判斷。

<br/>

<br/>

## header 作用
`X-Content-Type-Options` : 瀏覽器會自動猜每個資源的檔案類型(Content-Type)，例如: "text/javascript"，但有資安問題，攻擊者可以上傳含有JS程式碼的JPG檔案，並將圖片被載入到前端導致惡意腳本被執行，造成XSS攻擊。

`Content-Type-Options: nosniff` 表示防止瀏覽器亂猜造成資安問題，表示瀏覽器直接使用 header 中的 Content-Type，就不會有純文字、圖片被判斷成腳本，但缺點就是各種資源的 Content-Type 都需要設定好。


<br/>

<br/>

## References

> https://pjchender.dev/webdev/note-http-cache/
> https://ithelp.ithome.com.tw/articles/10226607
