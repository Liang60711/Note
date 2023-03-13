## 設置登入的用戶名及密碼
不使用 Spring Security 預設的帳號密碼 (user + uuid)，有3種方式。
1. 通過配置文件。
2. 通過配置類。
3. 自定義實作類(客製化，帳號資訊放在DB)。

<br/>

<br/>

## 1.通過配置文件
最簡單，使用 application.yml

```yml
spring:
  security:
    user: 
      name: root
      password: root
```

<br/>

<br/>

## 2. 通過配置類(推薦)

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 對密碼加密(通常不會這樣寫，示意)
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        String password = passwordEncoder.encode("root");

        auth.inMemoryAuthentication()
                .withUser("root")
                .password(password)
                .roles("admin");
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```