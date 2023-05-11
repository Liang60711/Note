# 針對權限或角色進行訪問控制

* 主要包含了4種方法

    * hasAuthority()
    * hasAnyAuthority()
    * hasRole()
    * hasAnyRole()

* 前兩個是依照`行為權限`，後兩個是依照`角色權限`

    * hasAnyAuthority("增", "刪", "改", "查")
    * hasAnyRole("普通用戶", "管理員")


<br/>

<br/>

## hasAuthority() 方法
用來設定`指定路徑`需要有`指定權限`(只能是單一權限，多權限要使用下一個方法)，才可訪問，以下以`是否有寫入權限`為例: 

1. 在 SecurityConfig 設定指定路徑和指定權限
    ```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/login.html").permitAll()
            .antMatchers("/read/**").hasAnyAuthority("READ") // 需要有 READ 權限，才能訪問 /read/** 路徑
            .anyRequest().authenticated()
    }
    ```

2. 在 UserDetailsService 實作類，把返回的 User 物件設置成有哪個權限，可以從 DB 中查。

    ```java
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        // 從DB查詢資料
        UserData userData = userDataRepository.findByUsername(s);
        String username = userData.getUsername();
        String password = userData.getPassword();
        String authType = userData.getAuthType();   //假如 authType = "READ"

        // 權限集合
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList(authType);

        // 返回 User 物件
        return new User(
            username, // 帳號
            new BCryptPasswordEncoder().encode(password), // 密碼
            auths   // 權限
        );
    }
    ```

3. 如果沒有權限訪問，會預設返回 `403 Forbidden`。

4. 補充，因為安全考量，`List<GrantedAuthority>` 如果想設置為成員變數，記得需要加上 `@JSONField(serialize = false)` 註解，因為安全考量，這個類`GrantedAuthority`是無法進行序列化的。


<br/>

<br/>

## hasAnyAuthority() 方法
只要有任何一種權限就可以訪問，與上一個 `hasAuthority()` 方法用法幾乎相同，只是此方法可以使用`複數權限`，需使用逗點分隔。
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
        .antMatchers("/login.html").permitAll()
        .antMatchers("/read/**").hasAnyAuthority("READ", "WRITE") // 多個權限，使用逗點分隔
        .anyRequest().authenticated()
}
```

UserDetailsService#loadUserByUsername()

```java
// 權限集合使用逗號分隔
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("READ,WRITE");
```

<br/>

<br/>


## hasRole() 方法
用來設定`指定路徑`需要有`指定角色`(只能是單一角色，多角色要使用下一個方法)，才可訪問，以下以管理者角色為例: 

1. 在 SecurityConfig 設定指定路徑和指定角色
    ```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/login.html").permitAll()
            .antMatchers("/admin/**").hasRole("ADMIN") // 需要有 ADMIN 角色，才能訪問 /admin/** 路徑
            .anyRequest().authenticated()
    }
    ```

2. 在 UserDetailsService 實作類，這邊要注意的是，跟 hasAuthority() 不同的是，角色須加上前綴 `ROLE_`，才能代表`角色`，否則就是指`權限`。

    ```java
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        // 從DB查詢資料
        UserData userData = userDataRepository.findByUsername(s);
        String username = userData.getUsername();
        String password = userData.getPassword();
        String role = userData.getRole();   //假如 role = "ADMIN"

        // 權限集合(角色前需要加上 ROLE_，以和權限做區分)
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_" + role)

        return new User(
            username,
            new BCryptPasswordEncoder().encode(password),
            getAuthority(userData)
        );
    }

    /** 取用戶的所有角色集合 */
    private Set<SimpleGrantedAuthority> getAuthority(UserData userData) {
        Set<SimpleGrantedAuthority> authorities = new HashSet<>();
        userData.getRoles().forEach(role -> {
            authorities.add(new SimpleGrantedAuthority("ROLE_" + role.getName()));
        });
        return authorities;
    }
    ```

3. 如果沒有該角色，會預設返回 `403 Forbidden`。

<br/>

<br/>

## hasAnyRole() 方法
同樣是 hasRole() 的複數版本，只要符合角色之一，就有權限進入

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
        .antMatchers("/login.html").permitAll()
        .antMatchers("/user/**").hasAnyRole("ADMIN", "USER") // 只要有 ADMIN 或 USER 角色，就能訪問 /user/** 路徑
        .anyRequest().authenticated()
}
```

UserDetailsService#loadUserByUsername()

```java
// 權限集合使用逗號分隔，並加上 ROLE_ 前綴
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_ADMIN,ROLE_USER");
```


<br/>

<br/>

## 自訂 "沒有權限訪問" 的頁面
預設是用 `403 Forbidden`，自定義的話，在配置類配置

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
        .antMatchers("/login.html").permitAll()
        .antMatchers("/user/**").hasAnyRole("ADMIN", "USER")
        .anyRequest().authenticated()
    .and()
        .exceptionHandling().accessDeniedPage("/403Page.html"); // 加上這一行即可
}
```
