# Jwt Token

## 概述

1. 尚未驗證(登入)前，只先開一個 驗證 endpoint。

    ```
    /auth/login
    ```

2. 驗證流程:

    1. 發起請求。

    2. JwtAuthenticationFilter，檢查是否有帶 token，結果沒帶，直接放行給下一個過濾器 `UsernamePasswordAuthenticationFilter`，

    3. 因為驗證 endpoint 有被設定 permitAll()，故會放行給 Controller 層。

    4. Controller 接收請求，將 username 和 password 封裝到 UsernamePasswordAuthenticationToken 物件，並用 AuthenticationManager.authenticate() 來驗證用戶信息，驗證成功(沒報錯)，生成 token 直接 response；若驗證失敗，返回 401。


3. 拿 token 取資料:

    1. 發起請求。

    2. JwtAuthenticationFilter，檢查是否有帶 token，結果有帶，直接解析 token，若解析成功(不報錯)，則呼叫 loadUserByUsername，並將驗證過的 Authentication 物件放進 SecurityContext 中，讓後面的過濾器鍊可以取用 (放進 SecurityContext 代表驗證成功)。


        ```java
        CustomUserDetails userDetails = myUserDetailsService.loadUserByUsername(username);
        Authentication authentication = 
                    new UsernamePasswordAuthenticationToken(
                    username, myUser.getPassword(), 
                    myUser.getAuthorities());

        SecurityContextHolder.getContext()
                                .setAuthentication(authentication);
        ```
    
    3. 呈上，若 token 解析失敗，則拋出錯誤，回 401。