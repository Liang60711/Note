## 設置登入的用戶名及密碼
不使用 Spring Security 預設的帳號密碼 (user + uuid)，有3種方式。
1. 通過配置文件(開發用)。
2. 通過配置類(開發用)。
3. 自定義實作類(真實情況，帳號資訊放在DB)。

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

<br/>

<br/>

## 自定義實作類(連DB)
Springboot 會先去找`配置文件` 和 `配置類`，如果以上兩個都沒有設定，會去找 `UserDetailsService` 接口。

1. 第一步，建立配置類，指定使用哪個 `UserDetailsService` 實現類。

  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {

      @Autowired
      private UserDetailsService userDetailsService;

      // 配置: 指定使用哪個 userDetailsService 物件，順便密碼encoder
      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception {
          auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
      }

      @Bean
      PasswordEncoder passwordEncoder() {
          return new BCryptPasswordEncoder();
      }
  }
  ```

2. 編寫 `UserDetailsService` 實現類，返回的 User 物件需有用戶密碼和操作權限。

  ```java
  @Service("userDetailsService")
  public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserinfoRepository userRepository;

    @Override
    public UserinfoDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        // 權限集合
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        // 從DB查詢資料
        Userinfo userinfo = userinfoRepository.findByUsername(s);// 從s參數傳進username供查詢

        // 如果用戶不存在，則抛出異常
        if (userinfo == null) {
            throw new UsernameNotFoundException("User not found with username: " + s);
        }
        

        String username = userinfo.getUsername();
        String password = userinfo.getPassword();
        // 返回 User 物件 (User 是 UserDetails 的實作，class User implements UserDetails，這邊取名有點奇怪)
        return new User(
            username, 
            new BCryptPasswordEncoder().encode(password), 
            auths
        );
    }
  }
  ```


  ```java
  // UserDetails 接口
  public interface UserDetails extends Serializable {
      Collection<? extends GrantedAuthority> getAuthorities();
      String getPassword();
      String getUsername();
      boolean isAccountNonExpired();
      boolean isAccountNonLocked();
      boolean isCredentialsNonExpired();
      boolean isEnabled();
  }
  ```

  <br/>

  <br/>


## 自定義登入頁面
自定義的登入頁面配置
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .formLogin()           // 自定義自己編寫的登入頁面
            .loginPage("/login")   // 輸入帳號密碼的頁面
            .loginProcessingUrl("/authenticate")  // 登入提交form表單後，會將該request提交給此路徑
            .defaultSuccessUrl("/index")    // 驗證成功轉導路徑
        .and()
            .authorizeRequests()   // 設置路徑是否需要通過驗證
            .antMatchers("/login").permitAll()   // 找匹配的路徑，並且不用驗證就可以訪問
            .anyRequest().authenticated()  // 其餘路徑皆須通過驗證，才允許訪問
        .and()
            .csrf().disable();   // 關閉 CSRF 保護機制(以便客户端可以在没有 CSRF token的情况下向服務器發送 POST、PUT、DELETE)
        
    }
}
```

前端 Form 的 action 屬性需要加上 `/authenticate`，Security 都會預設寫好，所以不用再去做此路徑的 Controller。

這邊還有一個重點，就是 input 的名稱一定要叫做 username 和 password。

```html
<form action="/authenticate" method="post">
    Username: <input type="text" name="username">
    Password: <input type="password" name="password">
    <input type="submit" value="Login">
</form>
```

<br/>

<br/>

## 自定義登出
簡易登出
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .logout()
        .logoutUrl("/logout")   // 執行logout的controller
        .logoutSuccessUrl("/index.html") // 成功logout後返回頁面
        .invalidateHttpSession(true)     // 刪除 HTTP Session(預設值為true，若使用false可能導致安全問題)
        .deleteCookies("JSESSIONID");    // 刪除 JSESSIONID cookie
}
```

使用自定義登出的 Handler
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .logout()
        .logoutUrl("/customLogout")
        .logoutSuccessHandler(new CustomLogoutSuccessHandler())
        .invalidateHttpSession(true)
        .deleteCookies("JSESSIONID");
}
```
自定義登出處理類 CustomLogoutSuccessHandler

```java
public class CustomLogoutSuccessHandler extends SimpleUrlLogoutSuccessHandler {
 
    @Override
    public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response,
            Authentication authentication) throws IOException, ServletException {
 
        // add your custom logic here
         
        super.onLogoutSuccess(request, response, authentication);
    }
}
```

<br/>

<br/>

## 自動登入 RememberMe
功能: 用戶只要登入後，可以關閉瀏覽器，下次打開瀏覽器還是在登入狀態。

基本原理: 主要是透過將 Token 儲存在 `Cookie` 中和`資料庫`中，下次用戶帶 Cookie 來訪問時，就可和資料庫中的 Token 做比對。

步驟: 
1. 用戶在 `UsernamePasswordAuthenticationFilter` 做登入驗證。
2. 驗證成功會交給 `RememberMeServices` 的實現類 `AbstractRememberMeServices` 做處理，並呼叫 `PersistentTokenRepository` 將 Token 寫入資料庫。
3. 用戶下次帶 Cookie 來訪問，在 `RememberMeAuthenticationFilter` 做驗證，讀取 Cookie 中的 Token。
4. `PersistentTokenRepository` 會撈取 DB 資料做比對，最後回 `UserDetailsService` 給授權。


<br/>

程式碼
1. 先建表(不用建entity類)，`JdbcTokenRepositoryImpl.CREATE_TABLE_SQL` 就有建資料表語句，表名為 `persistent_logins`。
2. 配置類，需設定數據來源。

    ```java
    @Configuration
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

        // 注入數據源
        @Autowired
        private DataSource dataSource;

        // 配置 TokenRepository，用來將 token 存入 DB
        @Bean
        public PersistentTokenRepository persistentTokenRepository() {
            JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
            jdbcTokenRepository.setDataSource(dataSource);
            //jdbcTokenRepository.setCreateTableOnStartup(true);  // 自動生成表，不用手動建表
            return jdbcTokenRepository;
        }
    }
    ```

3. HttpSecurity 配置

    ```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        .rememberMe()   // 開啟功能rememberMe
        .tokenRepository(persistentTokenRepository()) // 抓取 token 的配置
        .tokenValiditySeconds(60)   // 設置有效時長(秒)
        .userDetailsService(userDetailsService) // 用戶授權資訊(token匹配後，需載入授權資訊)
    }
    ```

4. 前端參數 : 與登入相同，input 的 name 必須叫做 `remember-me`，帳號的 name 叫 `username`，密碼的 name 叫 `password`。

    ```html
    <input type="checkbox" name="remember-me">自動登入
    ```

5. 可以檢查 : 有多一個名為 `remember-me` 的 cookie，資料表 `persistent_logins` 多一筆資料。


<br/>

<br/>
