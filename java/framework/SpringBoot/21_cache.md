## 緩存

1. 導入依賴

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
    </dependency>
    ```

2. 啟動類啟動緩存功能，`@EnableCaching`

    ```java
    @SpringBootApplication
    @EnableCaching
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

3. 在需要使用緩存的方法加上 `@Cacheable`

    ```java
    // value 屬性 => 此緩存的名稱，可以理解為一個獨立的記憶體區塊
    // key 屬性 => 在 userCache 此區塊中，是用什麼 key 去取值，這邊 #id 代表是 getUserById 的參數 id
    // 例如，id=1，就會在 userCache 區塊中找是否有 key 為 1 的緩存值，如果沒有就會將此 key-value 加到 userCache 區塊中

    @Cacheable(value = "userCache", key = "#id")
    public User getUserById(Long id) {
        return userRepository.findById(id);
    }
    ```

<br/>

<br/>

## Spring Cache 緩存框架

### 緩存框架解決了什麼問題

本地緩存和遠程緩存都有各自的缺點。

`本地緩存`: 如 Caffeine，優點是速度快，但是無法共享數據。

`遠程緩存`: 如 Redis，優點是可以共享數據，但是因為遠程連線、共享數據讀取量大，所以速度慢。

而 Spring Cache 集大成，同時提供本地緩存和遠程緩存，類似的框架還有 JetCache。

<br/>

<br/>

## @Catchable / @Cacheput 差別

這兩個註解是一個 interface，可以使用不同的套件來實現這兩個功能，如 Caffeine、EhCache、Redis 等等，需要在yml配置緩存套件。

`@Catchable` 當這個方法被調用時，Spring 會先檢查緩存中是否已經有了這個方法的結果，如果有就直接返回緩存中的結果(`不執行方法`)，否則才執行方法體並將結果存入緩存中。

`@Cacheput` 當這個方法被調用時，Spring 會直接執行方法並將結果存入緩存中。不會檢查緩存中是否已經有此 #key 的緩存(`每次皆執行方法`)。

差別:  
`@Catchable` 在方法執行前先檢查緩存，`@Cacheput` 在方法執行後將結果存入緩存。此外，@Catchable 僅在緩存中存在時才返回緩存結果，而 @Cacheput 總是執行方法體並將結果存入緩存。

舉例，隨機產生手機驗證碼，並存入緩存中:  
```java
// 建立
// 每次輸入 tele，都會產生隨機碼，並存入緩存中(為了防止每次隨機碼相同，故使用@CachePut，每次皆會執行方法)
@CachePut(value = "smsCode", key = "#tele")
public String setSmsCode(String tele) {
    return CodeUtils.getRandomCode(tele);
}
```

```java
// 獲取
// 如果找到 tele 緩存就返回緩存(不執行方法)，如果找不到 tele，就執行方法，返回null
@Cacheable(value = "smsCode", key = "#tele")
public String getSmsCache(String tele) {
    return null;    
}
```

#### `特別需要注意`:  
此兩個註解的方法`一定要使用 @Bean 來調用，否則Cache不會生效`，原因是 Cache 功能必須使用 Spring 容器的方法調用，如果不使用 @Bean 來調用，就等於不走 Spring 容器，而是普通方法的調用(同一個Bean內部調用不會經過Spring容器)，建議可以寫在獨立的一個 @Component 中，以達到使用@Bean 來調用。

```java
@Component
public class CacheUtils {

    @CachePut(value = "smsCode", key = "#tele")
    public String setSmsCode(String tele) {
        return tele;
    }

    @Cacheable(value = "smsCode", key = "#tele")
    public String getSmsCode(String tele) {
        return null;
    }
}
```


<br/>

<br/>

## SpringBoot 預設緩存

SpringBoot 預設使用的快取實作是基於 `ConcurrentHashMap` 的本機快取。這意味著如果在SpringBoot應用程式中使用了spring-boot-starter-cache依賴，但沒有進行任何特定的配置，Spring會預設使用ConcurrentHashMap作為快取提供者。這是一種簡單的本地緩存，資料儲存在應用的記憶體中。


若想要切換不同的緩存套件，可以使用以下配置:

```yml
spring:
    cache:
        type: caffeine
```