## 自定義失敗處理
包含 Forbidden(403) 或 Unauthorized(401) 的處理。

如果在驗證或授權過程中出現異常被 `ExceptionTranslationFilter` 捕獲到，此過濾器會去判斷是驗證失敗還是授權失敗。

* 如果是401的異常會被封裝成 `AuthenticationException`，並調用 `AuthenticationEntryPoint` 物件去做處理。

* 如果是403的異常會被封裝成 `AccessDeniedException`，並調用 `AccessDeniedHandler` 物件去處理。

<br/>

<br>

## AuthenticationEntryPoint
實作此接口，處理 401 的問題

```java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
        //...處理401異常
        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().print("401 Unauthorized");
    }
}
```

<br/>

<br/>

## AccessDeniedHandler
實作此接口，處理 403 的問題
```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
        //...處理403異常
        response.setStatus(HttpServletResponse.SC_FORBIDDEN);
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().print("403 forbidden");
    }
}
```



最後將上面這兩個實作類加到 SecurityConfig 配置中。

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .exceptionHandling()    // exception 處理
        .authenticationEntryPoint(authenticationEntryPoint) // 驗證失敗處理
        .accessDeniedHandler(accessDeniedHandler); // 授權失敗處理
}
```