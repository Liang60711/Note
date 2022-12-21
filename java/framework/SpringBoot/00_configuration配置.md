## Springboot 自動配置原理

`@SpringBootApplication` 有3個依賴的註解

```java
// 1.springboot 配置類
@SpringBootConfiguration
    @Configuration          // spring 配置類


// 2.自動導入 Package (最重要)
@EnableAutoConfiguration
    @AutoConfigurationPackage   // 自動配置package
        @Import(AutoConfigurationPackages.Registrar.class)    // 自動註冊package
    @Import(AutoConfigurationImportSelector.class)    // 自動導入pageage的核心

// 3.掃描當前主啟動類
@ComponentScan
```

* 原理: 

    springboot所有的自動配置都是在啟動的時候掃描並載入，但會判斷條件是否成立，只要在pom.xml導入對應的start依賴，就可以自動配置。

* `@EnableAutoConfiguration` 裡面的註解 `@AutoConfigurationPackage` 補充: 

    1. 將指定package下(這邊指 main 方法下所在的包名稱)，的所有component導入進來
    2. 並利用 `registry` 方法給容器導入一系列component。 

    ```java
    static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

		@Override
		public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
			register(registry, new PackageImports(metadata).getPackageNames().toArray(new String[0]));
            // new PackageImports(metadata).getPackageNames() 這邊就是main方法所在的包名稱，也就是整個專案的主package名稱
            // 並將此package名稱轉成 array，登記到 BeanDefinitionRegistry 實例當中。
		}

		@Override
		public Set<Object> determineImports(AnnotationMetadata metadata) {
			return Collections.singleton(new PackageImports(metadata));
		}
	}
    ```

* @Import(`AutoConfigurationImportSelector`.class)

    1. 會加載約144個自動配置類，詳見 `org.springframework.boot.autoconfigure.AutoConfiguration.imports`，裡面有所有的自動配置類




<br/>

<br/>

springboot 啟動流程

<img src="https://img-blog.csdnimg.cn/img_convert/6f0a27f6463098a371ff889f6ea8870b.png">



<br/>

<br/>

## FORM 表單開啟 PUT, PATCH, DELETE方法
`注意: 只有 form 表單會經過以下的 filter，如果使用 PostMan 或 API 直接打就不會過 filter。`


1.配置yml檔案
```yml
# 預設是關閉原因: 通常Springboot都是以微服務的形式，通常只會使用 get, post 的 API 需求，當要自己做前端頁面的時，才會有DELETE, PUT需求。
Spring: 
    mvc:
        hiddenmethod:
            filter:
                enabled: true   # 將此二方法開啟
```
2.前端Post form 使用 `_method`
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
public class HiddenHttpMethodFilter extends OncePerRequestFilter {

    private static final List<String> ALLOWED_METHODS;
    private String methodParam = "_method";

    // 此方法可以自定義 _method 的名稱
    public void setMethodParam(String methodParam) {
        Assert.hasText(methodParam, "'methodParam' must not be empty");
        this.methodParam = methodParam;
    }

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

如果需要修改 `name="_method"` 的名稱，可以自定義一個 Bean
```java
@Configuration(proxyBeanMethods = false)
public class WebConfig {

    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter() {
        HiddenHttpMethodFilter filter = new HiddenHttpMethodFilter();
        filter.setMethodParam("_m");// 類提供的公共方法
        return filter;
    }
}
```