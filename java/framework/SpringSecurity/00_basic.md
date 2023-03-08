## Spring Secirity 觀念
1. 主要做兩件事情: `驗證` 和 `授權`。
2. 與 Shiro 這個安全框架相比，Spring Security 功能較齊全，但相反 Shiro 比較輕量。


<br/>

<br/>

## 添加依賴
```xml
<dependencies>
    <!-- web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- security 主依賴 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>

<dependencies>
```

<br/>

<br/>

## 登入密碼
每次啟動 Server 都會在 console 中隨機產生密碼，在訪問路徑的時候需要輸入帳號密碼

帳號: `user`

密碼: `隨機產生`

<img src="../../../_image/Snipaste_2023-03-08_20-25-58.png">