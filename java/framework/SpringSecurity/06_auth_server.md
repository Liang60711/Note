## SSO 單一登入
因應微服務，需要有 SSO 架構來解決 SESSION-COOKIE 登入多次的缺點。

<br/>

<br/>

> goole 登入架構圖

<img src="https://img-blog.csdnimg.cn/93061090a9e64c9db46e9069782b0136.png">

<br/>

<br/>

## Springboot security 自建 Auth server

1. 加入maven依賴

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.apache.logging.log4j</groupId>
                <artifactId>log4j-to-slf4j</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    ```

2. 建立設定檔

    ```java
    @Configuration
    @EnableAuthorizationServer //主要註解
    public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    }
    ```

    上述 `@EnableAuthorizationServer` 註解源碼包含以下兩個配置類

    ```java
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Import({AuthorizationServerEndpointsConfiguration.class, AuthorizationServerSecurityConfiguration.class})// import兩個重要配置類
    public @interface EnableAuthorizationServer {

    }
    ```
    
    * `AuthorizationServerEndpointsConfiguration` 端點配置 : 裡面有配置了兩個 Bean，產生兩個重要的 endpoint

        1. AuthorizationEndpoint : 產生 `/oauth/authorize` 路徑，用來授權請求
        2. TokenEndpoint : 產生 `/oauth/token` 路徑，用來訪問並發放 token


    * `AuthorizationServerSecurityConfiguration` 安全配置 : 裡面配置了 UserDetailsService 和 ClientDetailsService

        1. UserDetailsService : 此 interface 唯一的方法 `loadUserByUsername()` 用來通過用戶名獲取用戶資訊(UserDetails)。
        2. ClientDetailsService : 此 interface 唯一的方法 `loadClientByClientId()` 用來通過客戶端ID獲取客戶端資訊(ClientDetails)。
        * 這兩個 Bean 很類似，一個是針對用戶(資源擁有者)，一個針對Client(要求權限的應用程式)。


3. 客製化 UserDetailsService

4. Auth Server 寫檢查 Token 的 Endpoint(給第5點使用)。

    ```java
    @Controller
    public class UserController {

        @GetMapping("/userinfo")
        @ResponseBody
        public Authentication getUserinfo(Authentication user) {
            return user;
        }
    }
    ```


5. 應用程式向 Resource owner 索取資料 (Header 帶上 Access Token)，而 Resource owner 會向 Auth Server 確認此 Token。

    ```yml
    security:
      oauth2:
          resource:
            user-info-uri: http://127.0.0.1:9999/auth/userinfo # 路徑自己設定，9999Port 為 auth server
    ```