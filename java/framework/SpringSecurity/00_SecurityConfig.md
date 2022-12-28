## 配置 SecurityConfig
* 在Springboot 2.3.7 版本中，使用 Spring security 5.4.x，已經不使用此 adapter 方式配置。
1. 此類必須繼承 `WebSecurityConfigurerAdapter` 抽象類，每實作一個 adapter，就會產生一條 filterChain 。
2. 此類的註解必須加上 `@EnableWebSecurity` `@Configuration`，讓安全配置生效。
3. 因繼承 `WebSecurityConfigurerAdapter`，有以下常見的3個 configure 可以覆寫。

    * `AuthenticationManagerBuilder` : 
        
        配置全域的驗證資訊，包含 AuthenticationProvider 和 UserDetailsService。

    * `WebSecurity` :

        配置全域忽略的規則、是否Debug、SpringFilterChain 配置、securityInterceptor、全局的HttpFirewall

    * `HttpSecurity` :

        配置各種具體的驗證機制規則


## HttpSecurity
requestMatchers() : 符合以下的路徑才會進 filter chain 判斷，如果沒有包含的路徑都會直接放行。  
authorizeRequests() : 
```java
@Override
public void configure(HttpSecurity http) throws Exception {
    http.requestMatchers().antMatchers(
                "/path1",  // 符合此2路徑才會進行以下的判斷
                "/path2",
                "/path3",
                "/path4"
        ).and().authorizeRequests().antMatchers(
                "/path1",  // 放行名單
                "/path4"
        ).permitAll()
        .anyRequest().authenticated();  // 其餘路徑皆須進行驗證
}
```


<br/>

<br/>

## 配置 SecurityConfig
新配置方式，適用 Spring Security 5.7 以上版本，捨棄 WebSecurityConfigureAdapter
```java
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf()
            .disable()
            //...

        return http.build();
    }

}
```
舊配置方式，使用 WebSecurityConfigureAdapter
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigureAdapter {

    @Override
    public void configure(HttpSecurity http) throws Exception {
    http.csrf()
        .disable()
        //...
}
}
```