## 配置 SecurityConfig
* 在Springboot 2.3.7 版本中，使用 Spring security 5.4.x，已經不使用 adapter 方式配置。
1. 若使用 adapter，此類必須繼承 `WebSecurityConfigurerAdapter` 抽象類。當每實作一個 adapter，就會產生一條 filterChain 。
2. 此類的註解必須加上 `@EnableWebSecurity` `@Configuration`，讓安全配置生效。
3. 因繼承 `WebSecurityConfigurerAdapter`，有以下常見的3個 configure 可以覆寫。

    * `AuthenticationManagerBuilder` : 
        
        配置全域的驗證資訊，包含 AuthenticationProvider 和 UserDetailsService。

    * `WebSecurity` :

        配置全域忽略的規則(通常只有使用此功能)、是否Debug、SpringFilterChain 配置、securityInterceptor、全局的HttpFirewall

    * `HttpSecurity` :

        配置各種具體的驗證機制規則

## WebSecurity
常用功能: 設定以下路徑不會經過 spring security 的 filter chain，代表這些路徑不用經過 spring security 的授權，直接可以讀取。
```java
@Override
public void configure(WebSecurity web) throws Exception {
    web
        .ignoring()
        .antMatchers("/resources/**")
        .antMatchers("/publics/**");
        // 此兩個路徑將不會受到spring security的驗證或授權
}

@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
        .antMatchers("/admin/**").hasRole("ADMIN")
        .antMatchers("/publics/**").hasRole("USER") // 上面已設定忽略此路徑，所以這行寫不寫都沒有影響
        .anyRequest().authenticated();
}
```

## HttpSecurity
requestMatchers() : 符合以下的路徑才會進 filter chain 判斷，如果沒有包含的路徑都會直接放行。  
authorizeRequests() : 指開始對 request 配置，後面需加上匹配器(matcher)。
```java
@Override
public void configure(HttpSecurity http) throws Exception {

    // 情況1
    http.authorizeRequests()
            .antMatchers("/a", "/b").permitAll()  // 此兩個路徑允許訪問
            .anyRequest().authenticated();  // 其餘路徑皆須通過驗證，才允許訪問

    // 情況2
    http.requestMatchers().antMatchers(
                "/path1",  // 符合此4路徑才會繼續進行以下的判斷，所以只有此四個路徑會進filter chain
                "/path2",
                "/path3",
                "/path4"
        )
        .and().authorizeRequests().antMatchers(
                "/path1",  
                "/path4"
        ).permitAll()  // 允許訪問
        .anyRequest().authenticated();  // 其餘路徑皆須通過驗證，才允許訪問
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

<br/>

<br/>

## @EnableGlobalMethodSecurity
1. 用於取代傳統 ApplicationContext.xml 中的配置 `<global-method-security>`
2. 此註解有三個屬性值
    * securedEnabled : 是否啟用 @Secured
    * prePostEnabled : 是否啟用 @PreAuthorize, @PostAuthorize
    * jsr250Enabled : 是否啟用 @RolesAllowed
3. @PreAuthorize, @PostAuthorize 這兩個註解才支援 SpringEL (Spring表達式)。

> https://blog.csdn.net/u011305680/article/details/80271423