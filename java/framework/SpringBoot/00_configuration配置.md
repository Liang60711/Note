## Springboot 自動配置原理

Springboot 預設會在底層配置好所有的component，但如果用戶自己配置了，則以用戶的優先(底層用了大量的 `@ConditionOnMissingBean`，代表如果用戶沒有配置則底層會自動配置。)

<br/>

<br/>

### `@SpringBootApplication` 有3個依賴的註解

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

* `@ComponentScan` 和 `@EnableAutoConfiguration` 差別

    * `@ComponentScan` : 掃描 @SpringBootApplication 所在的 Application 類 (即spring-boot的入口類) 所在的包 (basepackage) 下所有的 @component註解 (或拓展了@component的註解) 標記的bean，並註冊到spring容器中。
 

    * `@EnableAutoConfiguration` : 加載資源目錄 META-INF 文件下的 `spring.factories` 文件。包括所有 Jar 包下的 /META-INF/spring.factories 文件。spring.factories 文件是一个 `properties` 文件。

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

    2. 這些自動配置類會依照需求判斷是否需要加載

        ```java
        @AutoConfiguration
        @ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true) // matchIfMissing，若沒有實例則創建一個
        public class AopAutoConfiguration {
        }
        ```



<br/>

<br/>

springboot 啟動流程

<img src="https://img-blog.csdnimg.cn/img_convert/6f0a27f6463098a371ff889f6ea8870b.png">

<br/>

<br/>

## 如果需要做自定義的配置時，可以按照以下步驟
1. 查詢自動配置類的jar包 `spring-boot-autoconfigure-2.7.3.jar` 
2. package會依照功能區分，例如 cache/CacheAutoConfiguration，可以找到相關可以配置的 `@Bean`，類的名稱必須為`AutoConfiguration`。
3. 若要找 yml 配置的 prefix 名稱，可以找到 `xxxxxProperties` 檔案中尋找。

    ```java
    // 可找到 prefix.cache 以下的所有屬性
    @ConfigurationProperties(prefix = "spring.cache")
    public class CacheProperties {
        //...
    }
    ```

4. 補充: `@ConfigurationProperties` 此註解除了用在 class 上，還可以運用在 @Bean 方法上，同樣是返回指定 prefix 的物件。

    ```java
    @Bean
    @ConfigurationProperties(prefix = "customClass")
    public CustomClass customClass() {
        return new CustomClass();
    }
    ```

5. 補充2: `@EnableConfigurationProperties`，此註解可以將使用 `@ConfigurationProperties` 這個註解的類加入到 Spring 容器，用處是寫法風格不會這麼鬆散。

    ```java
    // 簡單來說，就是會將 ServerConfig 建立一個 bean，並加入到Spring容器中
    @EnableConfigurationProperties(ServerConfig.class)
    public class DemoApplication {

    }
    ```
    被加入的類
    ```java
    // 由於@EnableConfigurationProperties已經會將此類加入容器了，這邊如果再加上@Compoent就會報錯，因為容器中有兩個相同的Bean
    @Data
    @ConfigurationProperties(prefix = "servers")
    public class ServerConfig {

    }
    ```

<br/>

<br/>

## @configurationproperties 支持鬆散綁定

`@configurationproperties` 支持鬆散綁定，讓 yml 中的不同書寫的風格都可以被讀取，只有此註解有支援，`@Value` 就沒有支援。

綁定規範: 僅能使用純小寫字母、數字、底線作為合法字元
```java
@configurationproperties(prefix = "serverinfo")//只能全小寫，不能使用 serverInfo 
public class ServerConfig {
    private String ipAddress;
}
```

可支援的寫法有

```yml
# camel case
serverInfo:
  ipAddress: 192.168.1.1
```
```yml
# snake case
serverInfo:
  ip_address: 192.168.1.1
```
```yml
# dash 這種寫法是最推薦的官方寫法
serverInfo:
  ip-address: 192.168.1.1
```
```yml
# CONST
serverInfo:
  IP_ADDRESS: 192.168.1.1
```

<br/>

<br/>

## @ConfigurationProperties 常用單位換算 @DurationUnit
在 yml 中使用的單位，例如毫秒，可以在配置類中切換成其他單位

```yml
servers:
  serverTimeout: 3
```
配置類中設置單位

```java
@Data
@ConfigurationProperties(prefix = "servers")
public class ServerConfig {

    @DurationUnit(ChronoUnit.HOURS) // 自定義單位
    private Duration serverTimeout;
}
```

<br/>

<br/>

## @ConfigurationProperties 容量單位換算 @DataSizeUnit
同上，容量也可以換算
```yml
servers:
  dataSize: 10  # 預設單位是Byte
```
配置類中設置單位
```java
@Data
@ConfigurationProperties(prefix = "servers")
public class ServerConfig {

    @DataSizeUnit(DataUnit.GIGABYTES) // 自定義單位
    private DataSize dataSize;
}
```

另一種方式是在 yml 直接寫單位，但是打印出來還是用 Byte 為單位

```yml
servers:
  dataSize: 10MB # 打印出來會是 10485760B，相當於 1024*1024*10 B，不會打印出 10MB
```


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

<br/>

<br/>

## spring.factories 檔案
* 當 Springboot 要引入某個功能的時候，只需要在 maven 或 gradle 的配置中直接引入對應的 Starter，馬上就可以使用了，而不需要像傳統 Spring 應用那樣寫 xml 或 java 配置類來初始化各種 Bean。

* 上述可以自動載入 Bean 的原理是 : 每當 Spring Boot 應用啟動的時候，會讀取ClassPath中的 `/META-INF/spring.factories` 文件 和每個 Jar 包中的 `/META-INF/spring.factories` 文件，就會依照所指定的配置類去加載 Bean。

* factories 檔案是一種 properties 格式的檔案。

<br/>

<br/>

## Configuration 內部類
當外部類和內部類都加上 @Configuration 的原因，原因是內部類中的 @Bean 如果要生效，就必須加上 @Configuration，而外部類的 @Configuration 不會延伸到內部類中，故需要在內部類中再加上一次。
```java
@Configuration
public class Outter {

    @Configuration
    public static class Inner {
        
        @Bean
        public User getUser() {
            //...
        }
    }
}
```