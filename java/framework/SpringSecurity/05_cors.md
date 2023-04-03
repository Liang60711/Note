## 跨域
* 跨域問題是基於瀏覽器的規範，Postman是無法實測出跨域問題的。
* 瀏覽器基於安全因素，使用 `XMLHttpRequest` 物件發起 HTTP 請求時，必須遵守同源策略。
* 同源策略 = Protocol + Domain name + Port 都需要一致。
* 前後端分離的項目，一般都不是同源的，所以肯定會存在跨域請求的問題。

<br/>

<br/>

先對 Springboot 配置

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        
        registry.addMapping("/**")  // 設置允許跨域的路徑
                .allowedOrigins("*") // 設置允許跨域請求的域名
                .allowCredentials(true) // 是否允許cookie
                .allowedMethods("GET","POST","DELETE","PUT") // 設置允許的請求方式
                .allowedHeaders("*")  // 設置允許的header屬性
                .maxAge(3600);  // 跨域允許時間
    }
}
```

spring security 配置

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    
    // 允許跨域
    http.cors();
}
```