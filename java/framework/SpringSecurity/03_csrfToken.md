## CSRFToken

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