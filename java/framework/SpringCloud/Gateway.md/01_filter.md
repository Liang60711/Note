## 自定義 GatewayFilterFactory
目的: 可以用來使用在 routes 中的 filter

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: circuitbreaker_route
          uri: http://localhost
          predicates:
            - Path=/ws
          filters:
            - CustomFilter # 自定義的Filter

```

重點: 
1. 類名稱必須叫做 `XXX`GatewayFilterFactory，注入到 spring 容器中的名稱就叫做 `XXX`。

2. 類必須繼承 AbstractGatewayFilterFactory。
3. 所有需要傳遞進來的參數都配置到內部類 Config 中。

