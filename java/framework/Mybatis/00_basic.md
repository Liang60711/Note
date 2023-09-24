## 各種使用場景

1. 單表查詢:

    - 使用 mybatis generator
    - 使用 mybatis plus

2. 多表關聯查詢:

    - 使用 XML 方式

3. SQL不長、單表查詢

    - 使用 @Select 註解方式(寫在 XXXMapper interface 當中)


<br/>

<br/>

## MapperScan
會自動掃描package下的Bean檔案

```java
@SpringBootApplication
@EnableCaching
@MapperScan(basePackages = {"com.example.**.mapper"}) // 在所有package下都可以掃描到
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```