## 設定
`pom.xml`加入dependency
```xml
<!-- jdbc功能 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<!-- mysql driver -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.22</version>
</dependency>
```

`application.properties`
```properties
# 選擇使用的driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 連線的url
# 後面的query可帶可不帶
spring.datasource.url=jdbc:mysql://localhost:3306/myjdbc?serverTimezone=Asia/Taipei&characterEncoding=utf-8
# mysql 登入資訊
spring.datasource.username=root
spring.datasource.password=root
```