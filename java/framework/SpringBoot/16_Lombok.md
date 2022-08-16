## 設定

`pom.xml`

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```



## @Getter / @Setter

* 透過添加註解去自動生成對應的程式，使程式碼更簡潔。
  
  ```java
  @Getter
  @Setter
  public class Student {
      Integer id;
      String name;
  }
  ```
  
  ```java
  // 自動生成 getter 和 setter，並複寫 toString() 將所有變數印出
  @Data
  public class Student {
      Integer id;
      String name;
  }
  ```

## 



## @Slf4j

1. 目的:
   
   - 為了少寫這行代碼
   
   - ```java
     private static final Logger logger = LoggerFactory.getLogger(this.XXX.class);
     ```
   
   - 使用: 直接使用 log物件
     
     ```java
     // 原本寫法
     logger.info("msg");
     
     // 使用@Slf4j
     log.info("msg");
     ```

2. 添加依賴
   
   ```xml
   <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-log4j12</artifactId>
     <version>1.7.28</version>
   </dependency>
   ```





## @NoArgsConstructor, @AllArgsConstructor, @RequiredArgsConstructor

這三個都是產生 constructor，只差生成的參數不同



1. **@NoArgsConstructor** : 生成一個沒有參數的constructor
   
   ```java
   @NoArgsConstructor
   public class User {
       private Integer id;
       private String name;
   }
   
   
   // 相當於
   public class User {
       private Integer id;
       private String name;
       public User() {}
   }
   ```

2. **@AllArgsConstructor** : 生成一個包含所有參數的 constructor  

    ```java
      @AllArgsConstructor
      public class User {
        private Integer id;
        private String name;
    }

    // 相當於
    public class User {
        private Integer id;
        private String name;
        public User(Integer id, String name) {
            this.id = id;
            this.name = name;
        }
    }
    ```
* 注意: `@AllArgsConstructor` 只會加上有參數的constructor，因為很多地方需要加上空參的constructor (如Spring data jpa)，所以應該要這樣寫才不會報錯

  ```java
  @AllArgsConstructor
  @NoArgsConstructor
  public class User {
      private Integer id;
      private String name;
  }
  ```

3. **@RequiredArgsConstructor** : 生成一個包含 "特定參數" 的 constructor，特定參數指的是那些有加上 final 修飾詞的變量，如果所有的變量都是正常的，都沒有用 final 修飾的話，那就會生成一個空參的 constructor。

  ```java
  @RequiredArgsConstructor
  public class User {
      private final Integer id;
      private String name;
  }

  // 相當於
  public class User {
      private Integer id;
      private String name;
      public User(Integer id) {
          this.id = id;
      }
  }
  ```

<br/>

<br/>

## @Data
等於以下這幾個合併
1. @Getter/@Setter
2. @ToString
3. @EqualsAndHashCode
4. @RequiredArgsConstructor

```java
@Data
public class User {
    private Integer id;
    private String name;
}
```

<br/>

<br/>

## @Value
會把所有變數都設為 `final`，其他功能與 `@Data` 相同
1. @Getter (沒有setter，因為設為final)
2. @ToString
3. @EqualsAndHashCode
4. @RequiredArgsConstructor