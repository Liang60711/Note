## 跨域配置
網關可以使用配置檔，即可簡單實現跨域的配置，若有特殊配置，需要使用 @Bean 去配置
```yml
spring:
  cloud:
    gateway:
      globalcors: # 全域的跨域處理
        add-to-simple-url-handler-mapping: true # 解決 HTTP OPTIONS 請求被攔截的問題(預檢時允許瀏覽器使用OPTIONS詢問網關)
        cors-configurations:
          '[/**]':  # 攔截那些路徑
            allowedOrigins: # 允許哪些網站的跨域請求
              - "http://localhost:8080"
              - "https://www.demo.com"
            allowedMethods: # 允許的跨域ajax的請求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允許再請求中攜帶的 header
            allowCredentials: true # 是否允許攜帶 Cookie
            maxAge: 3600 # 跨域允許時間(時間過了重新預檢)
```
