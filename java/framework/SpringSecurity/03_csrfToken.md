## CSRFToken

前後端分離的專案本身就需要在 header 中加入 token，與 csrf token 的功能類似，因此前後端分離項目本身就不怕 csrf 攻擊，因此需要關閉 Spring security 功能中的 CSRF token。

在 Spring Security 中設置 CSRF token 需要做以下幾個步驟：

1. 在 Spring Security 的配置類別中啟用 CSRF 防護，開發時會關閉，但 SpringBoot 預設是打開的。

    ```java
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            // http.csrf().disable();
        }
    }
    ```

2. 前端頁面若使用 thymeleaf，可以使用此範例， `${_csrf.parameterName}` 會在前端渲染成 `_csrf`，而 `${_csrf.token}` 會是一串 uuid。

    ```html
    <form method="post" action="/">
        <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
        <!-- 其他表單欄位 -->
        <button type="submit">Submit</button>
    </form>
    ```