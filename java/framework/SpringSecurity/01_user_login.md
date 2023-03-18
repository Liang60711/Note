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