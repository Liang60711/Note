## Eureka 發現中心

1. Spring Cloud Netflix Eureka（簡稱Eureka），服務註冊及發現框架
2. 解決問題 : 
    
    * 主要用於在`分佈式系統中解決服務發現的問題`。在分佈式系統中，服務之間經常需要進行通信，但是這些服務的位置和 IP 地址可能會隨時改變，因此需要一種機制來發現和確定服務的位置。

    * 可支援 High Available。

3. Eureka 的主要目標是實現服務的自我發現和自我保護。當新的服務被部署或現有的服務發生變化時，Eureka 可以使其它服務和客戶端動態地發現這些變化，並且避免了硬編碼IP地址和端口的問題。這樣，服務之間的通信變得更加靈活和容易管理。

4. `Eureka Server` 為註冊及發現中心，其他向此 server 註冊的服務稱為 `Eureka Client`。

5. 發現中心架構

    <img width="70%" src="https://img-blog.csdnimg.cn/20210303182443816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MTUwMzU2,size_16,color_FFFFFF,t_70">

<br/>

<br/>

## Eureka Server
創建步驟如下

1. 導入依賴

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    ```

2. 啟動註解

    ```java
    @EnableEurekaServer // 將此服務定義為 Eureka Server
    @SpringBootApplication
    public class EurekaApplication {

        public static void main(String[] args) {
            SpringApplication.run(EurekaApplication.class, args);
        }

    }
    ```

3. yml配置

    ```yml
    server:
        port: 1111 # Eureka Server 起的 port

    eureka:
        instance:
            hostname: localhost # Eureka Server 服務名稱。
        client:
            register-with-eureka: false # 是否把自己註冊到 Eureka Server，因為本身就是Eureka Server，所以設為false。
            fetch-registry: false # 是否從 Eureka Server 取得註冊資訊，因為本身就是Eureka Server，所以設為false
            service-url:
                default-zone: http://${eureka.instance.hostname}:${server.port}/eureka/ # Eureka Server的IP
    ```

4. 可以在 Eureka 中選擇是否加入 Spring Security，並配置 filter 規則

    ```java
    @EnableWebSecurity
    class WebSecurityConfig extends WebSecurityConfigurerAdapter {

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.csrf().ignoringAntMatchers("/eureka/**");
            super.configure(http);
        }
    }
    ```

<br/>

<br/>

## Eureka Client 
創建步驟如下
1. 導入依賴

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    ```

2. 啟動註解

    ```java
    @EnableDiscoveryClient  // 將此服務定義為 Eureka Client
    @SpringBootApplication
    public class SpringCloudEurekaClientDemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(SpringCloudEurekaClientDemoApplication.class, args);
        }

    }
    ```

3. yml配置

    ```yml
    # Spring properties
    spring:
        application:
            name: eureka-client-service # 服務名稱
    # Discovery Server Access
    eureka:
        client:
            service-url:
                default-zone: http://localhost:8761/eureka/ # Eureka Server IP
    # HTTP Server
    server:
        port: 8080   # 服務的 port
    ```

4. 需要先啟動 Eureka Server，再啟動註冊的服務 (Eureka Client)，否則會報錯，可以連到 Eureka Server 去看當前的註冊服務有哪些。

<br/>

<br/>

## 將 Gateway 註冊到 Eureka 配置

1. Eureka 註冊中心依賴(Eureka Server)、Gateway 加上依賴(Eureka Client)

2. 配置類 Eureka Server 上加上註解 @EnableEurekaServer，Client 似乎是不需要加 @EnableDiscoveryClient

3. Gateway yml配置

    ```yml
    server:
        port: 80

    spring:
        application:
            name: api-gateway   # 註冊在 Eureka Server 的 ID (不能使用底線命名)
        cloud:
            gateway:
                discovery:
                    locator:
                        enabled: true # 開啟 Gateway 服務註冊中心服務發現
    eureka:
        client:
            service-url:
                defaultZone: http://localhost:8761/eureka/  # Eureka Server 位址
            enabled: true   # 開啟 Eureka Client
    ```

4. 測試，需要啟3個服務: Eureka Server、Gateway、測試用Application，新的 url 若可以連線，代表測試成功。

    ```
    原url: http://原服務位址：端口/{controller路徑}

    新url: http://網關位址：端口/{註冊中心server id}/{controller路徑}
    ```



<br/>

<br/>

## Reference

> https://docs.spring.io/spring-cloud-netflix/docs/3.1.8-SNAPSHOT/reference/html/

> https://matthung0807.blogspot.com/2019/06/spring-cloud-spring-cloud-netflix-eureka.html

> https://blog.csdn.net/qq_39150356/article/details/114327998