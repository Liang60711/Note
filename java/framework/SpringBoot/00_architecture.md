## Spring Boot Parent 定義
1. Springboot 繼承了 `spring-boot-starter-parent`，在此類中，有 dependencies 屬性，定義了許多常用套件的版本號。
2. 之所以會定義是為了

    * 避免多個依賴使用相同技術時會出現依賴版本衝突。


<br/>

<br/>

## Spring Boot starter
1. 開發 spring boot 需要導入依賴時通常會導入對應的 starter。
2. 每個 starter 裡面通常包含多個依賴。
3. 用途是為了 `簡化配置`。

<br/>

<br/>

## Spring Boot 內嵌服務器
1. spring boot 內嵌 tomcat，`spring-boot-starter-web` 中的依賴有包含 `spring-boot-starter-tomcat`。
2. tomcat 是使用 java 寫的服務器，那將此 java 物件也交給 spring 容器保管。
3. 可以將 tomcat 更換成其他內嵌服務器

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
    ```

<br/>

<br/>

## Java Model 與 Entity 的區別
>https://matthung0807.blogspot.com/2019/04/java-modelentity.html

<br/>

<br/>

## Dao 與 Repository 的區別
* Dao 
    1. 屬於持久層的抽象概念。
    2. 較底層的抽象概念，接近資料庫。


* Repository : 
    1. 為物件之間的聚合 (aggregate)。
    2. 相對於 Dao 層，更接近物件。


<Br/>

<br/>

## windows 中強制 關閉 port 號

假設要關閉 8888 port
```sh
# 找 8888 Port
netstat -ano | findstr :8888

# 找到右側的PID (例如是18872)
taskkill /PID 18872 /F
```

<br/>

<br/>

## SpringBoot 中的 Context 關係
