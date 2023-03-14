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

      @Override
      public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

          // 權限集合
          List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
          // 從DB查詢資料
          UserData userData = userDataRepository.findByUsername(s);
          String username = userData.getUsername();
          String password = userData.getPassword();
          // 返回 User 物件 (User 是 UserDetails 的實作，class User implements UserDetails，這邊取名有點奇怪)
          return new User(username, new BCryptPasswordEncoder().encode(password), auths);
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