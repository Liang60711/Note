# 基於角色或權限進行訪問控制

## hasAuthority() 方法
用來設定`指定路徑`需要有`指定角色`(只能是單一角色，多角色要使用下一個方法)，才可訪問，以下以管理者角色為例: 

1. 在 SecurityConfig 設定指定路徑和指定角色
    ```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/login.html").permitAll()
            .antMatchers("/admin/**").hasAnyAuthority("admin") // 需要有 admin 角色，才能訪問 /admin/** 路徑
            .anyRequest().authenticated()
        .and()
            .csrf().disable();

    }
    ```

2. 在 UserDetailsService 實作類，把返回的 User 物件設置是什麼角色，可以從DB中查。

    ```java
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        // 從DB查詢資料
        UserData userData = userDataRepository.findByUsername(s);
        String username = userData.getUsername();
        String password = userData.getPassword();
        String role = userData.getRole();   //假如 role = "admin"

        // 權限集合
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList(role);

        // 返回 User 物件
        return new User(
            username, // 帳號
            new BCryptPasswordEncoder().encode(password), // 密碼
            auths
        );
    }
    ```

3. 如果沒有權限訪問的路徑，會預設返回 `403 Forbidden`。


<br/>

<br/>

## hasAnyAuthority() 方法
與上一個 `hasAuthority()` 方法用法幾乎相同，只是此方法可以使用`複數角色`，需使用逗點分隔。
```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/login.html").permitAll()
            .antMatchers("/admin/**").hasAnyAuthority("admin,manager") // 多個角色，使用逗點分隔
            .anyRequest().authenticated()
        .and()
            .csrf().disable();

    }
```