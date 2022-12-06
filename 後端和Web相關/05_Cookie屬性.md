## Cookie
為伺服器傳送予使用者瀏覽器的一個小片段資料，瀏覽器可能儲存並於下一次請求回傳 cookie 至相同的伺服器。

<br/>

<br/>

## Cookie 屬性
> https://ithelp.ithome.com.tw/articles/10227602

1. `Domain`：Cookie 的有效 domain，如果未設定，就會自動綁在執行 Set-Cookie 的 domain 下；雖然可以自行設置，但其實也只能在一級/次級網域之間調整，寫到其他人的 domain 是寫不進去的。
2. `Path`：可以指定 Cookie 只在特定路徑下生效，未設定預設為 '/'。
3. `Max-Age`：有效期限，單位是秒；當數值為正數時有效，負數時為本次 Session 有效；0 為刪除 Cookie。
4. `Expires`：同上，只是指定的是時間點，例如: 2022/08/05。
5. `Secure`：這個屬性被設為 true 時，此 Cookie 就只會在「安全的協議」下傳輸，通常為 HTTPS。
6. `HttpOnly`：只能在網路傳輸中使用，當設為 true 時，此 Cookie 就無法在任何 JavaScript 程式碼中取得。
7. `SameSite` : 跨網域存取，有以下3種

    * Strict : 最為嚴格，只有 domain 相同才能發送。
    * Lax : 使用 GET 時，允許跨網域；使用 POST 時，不允許。
    * None : 允許跨網域存取，但是若沒 HTTPS 則預設「拒絕」跨網域存取。



<br/>

<br/>

## Session Cookie / Persistent Cookie
`Session Cookie` : 未設定 Max-Age 或 Expires 屬性，瀏覽器關閉後即會消失。

`Persistent Cookie` : 持久性的，關閉瀏覽器不會消失，直到到期時間才消失。