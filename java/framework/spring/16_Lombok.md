## Lombok
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


## 設定

`pom.xml`
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```