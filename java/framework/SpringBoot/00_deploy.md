## 在 linux 啟動 Springboot 服務

1. 使用 maven `package` 打包成 jar ，需要注意是否有以下依賴(springboot的和)

    ```xml
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
    ```

    打包可跳過 test 步驟

    <img src="../../../_image/Snipaste_2023-03-01_20-26-40.png">

2. 啟動服務

    ```sh
    java -jar demo-0.0.1-SNAPSHOT.jar
    ```

3. 更改臨時屬性，只有此次啟動有效，臨時屬性的優先級會高於配置檔案。 `--` 後面使用 properties 屬性的寫法。

    ```properties
    server.port=8888
    ```

    ```sh
    java -jar demo-0.0.1-SNAPSHOT.jar --server.port=8888
    ```

4. 在 IDEA 中，使用`臨時屬性`

    <img src="../../../_image/Snipaste_2023-03-01_20-32-38.png">
    <img src="../../../_image/Snipaste_2023-03-01_20-34-19.png">
    <img src="../../../_image/Snipaste_2023-03-01_20-34-52.png">



* 補充: 其實臨時屬性會在入口方法中作為參數。

    ```sh
    java -jar demo-0.0.1-SNAPSHOT.jar --server.port=8888 --a=b
    ```
    ```java
    public static void main(String[] args) {
        
        System.out.println(Arrays.toString(args));// [--server.port=8081, --a=b]

        SpringApplication.run(DemoApplication.class, args);
    }
    ```
    所以可以將入口方法改成以下，就等於限制`不能使用臨時屬性`(安全考量)
    
    ```java
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class);//不放args，即不使用臨時屬性
    }
    ```