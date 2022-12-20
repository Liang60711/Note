## 靜態資源配置

* 靜態資源的訪問，默認是 `/resources/**`，如果有 `webapp/WEB-INF` 目錄，則使用此路徑作為根目錄。

* 源碼在 `WebMvcAutoConfiguration` 這個自動配置類中。

* 補充: 如果`API路徑(controller route)`與靜態資源相同，則會先去訪問`API路徑`(硬要測試的話)。



```yml
spring:
    mvc:
        # 使用路徑前綴
        static-path-pattern: /prefix/** #這樣訪問路徑即為/prefix/resources/**
    
    web: # <-- 舊版本沒有web階層
        resources:
            # 更改靜態資源路徑根目錄(所以根目錄為resources/rootPath)
            static-locations: classpath:/rootPath/
            # 禁用靜態資源路徑配置，效果就是所有的靜態資源都無法訪問
            add-mappings: true  #預設為true，禁用為false
            # 設置靜態資源緩存時間(11000秒)
            cache:
                period: 11000
```

<img src="https://user-images.githubusercontent.com/63166397/204546300-859ff0f9-d002-4ffb-a2d6-257171e4e38a.png" width="50%">

有了Cache後，Status code 為304，並且Request Header會產生 If-Modified-List

<img src="https://user-images.githubusercontent.com/63166397/204546386-c252e9d5-c644-48cb-b858-5b9666fbaf40.png" width="50%">


<br>

<br/>

## FORM 表單開啟 PUT, PATCH, DELETE方法
`注意: 只有 form 表單會經過以下的 filter，如果使用 PostMan 或 API 直接打就不會過 filter。`


配置檔案
```yml
# 預設是關閉原因: 通常Springboot都是以微服務的形式，通常只會使用 get, post 的 API 需求，當要自己做前端頁面的時，才會有DELETE, PUT需求。
Spring: 
    mvc:
        hiddenmethod:
            filter:
                enabled: true   # 將此二方法開啟
```
前端Post form
```html
<form action="/" method="post">
    <!-- 記得用 _method -->
    <input type="hidden" name="_method" value="DELETE">
    <input type="submit" value="提交delete">
</form>
```

源代碼在 `HiddenHttpMethodFilter` 和 `WebMvcAutoConfiguration` 兩個類。
```java
// HiddenHttpMethodFilter 過濾所有 request

private String methodParam = "_method";

static {
    ALLOWED_METHODS = Collections.unmodifiableList(
        Arrays.asList(
            HttpMethod.PUT.name(), 
            HttpMethod.DELETE.name(), 
            HttpMethod.PATCH.name()
        )
    );
}

protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
    HttpServletRequest requestToUse = request;
    // 如果使用 POST
    if ("POST".equals(request.getMethod()) && request.getAttribute("javax.servlet.error.exception") == null) {
        String paramValue = request.getParameter(this.methodParam);// this.methodParam 就是上面的成員屬性 _method
        if (StringUtils.hasLength(paramValue)) {
            String method = paramValue.toUpperCase(Locale.ENGLISH);
            // ALLOWED_METHODS包含 static 中的三種 http 方法
            if (ALLOWED_METHODS.contains(method)) { 
                // 如果是的話就用 wrapper 包起來，包過的方法就是 _method 所用的方法
                requestToUse = new HttpMethodRequestWrapper(request, method);
            }
        }
    }

    filterChain.doFilter((ServletRequest)requestToUse, response);
}
```
```java
// WebMvcAutoConfiguration
// @ConditionalOnMissingBean 先呼叫了上面的 filter，所以這邊的 request.getMethod() 都是已經是 Wrapper 過的新 HTTP 方法。

@Bean
@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled") // 使用屬性在這
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new OrderedHiddenHttpMethodFilter();
}
```