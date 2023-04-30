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