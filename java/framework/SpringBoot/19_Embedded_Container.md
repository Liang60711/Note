## Jetty
添加依賴
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- 排除tomcat -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

Java配置類，可寫在 `@Configuration` 或是 `啟動類`。

```java
@Bean
public JettyServletWebServerFactory servletContainer() {
    JettyServletWebServerFactory factory = new JettyServletWebServerFactory();
    factory.setPort(8080);
    factory.addConfigurations(new AbstractConfiguration() { // 配置額外選項
        @Override
        public void configure(WebAppContext context) {
            context.getServletContext().getSessionCookieConfig().setSecure(true);
            context.getServletContext().getSessionCookieConfig().setHttpOnly(true);
        }
    });

    return factory;
}
```


<br/>

<br/>

## Undertow

添加依賴

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- 排除tomcat -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

<br/>

<br/>