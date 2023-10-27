## 概述
1. mybatis-plus 是以 mybatis 為基礎建構的框架，不會去更動原有 mybatis 的代碼。

2. mybatis plus 和 mybatis generator 是可以存在於同一個項目中的，但因為命名的關係(Mapper類命名可能重複)，因此建議只使用一種框架，或是規範命名方式。

<br/>

<br/>

## 配置

1. 添加依賴，將 mybatis 修改為 mybatis-plus

    ```xml
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>
    ```

    更改為

    ```xml
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.3.2</version>
    </dependency>
    ```

2. 配置，只需要加入數據源

    ```yml
    datasource:
        url: jdbc:mysql://localhost:3306/security?serverTimezone=GMT&useSSL=false
        driver-class-name: com.mysql.jdbc.Driver
        username: root
        password: root
    ```

3. 啟動類加上 @MapperScan

    ```java
    @SpringBootApplication
    @EnableCaching
    @MapperScan(basePackages = {"com.example.demo.mapper"}) // 包名為mapper
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }

    }
    ```


4. 寫 Model 物件，相當於 JPA 的 Entity

    ```java
    @Builder
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Userdata {

        private String username;

        private String password;

        private String roles;

        private Integer id;
    }
    ```

5. 寫 Mapper 接口，相當於 JPA 的 @Repository 接口

    ```java
    public interface UserdataMapper extends BaseMapper<Userdata> {

    }
    ```