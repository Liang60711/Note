## Oauth2.0
> https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015


* 流程:

    1. 由 Application 頁面點選第三方登入連結，此連結會前往登入頁面，此連結的 Query String 通常會帶幾種參數。

        ```
        https://accounts.google.com/?redirect=xxx&type=token&scope=email,profile
        ```
    
    2. 當登入後(身分驗證成功)，會回給 Application 一個 Token，此 Token 以 `JWT` 的格式回傳。

    3. Application 就可以使用此 Token 去存取第三方登入平台所提供的的相關資訊 (會有 scope 限制存取範圍)。