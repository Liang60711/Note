## JUnit
1. 單元可以自動化測試一個方法，或是一個 API。
2. 單元測試互相獨立，不能有依賴關係。
3. 注意事項
    * 測試程式放在test目錄中。
    * 測試 class 名稱以`被測試class` + `Test` 命名。
    * 測試 class 的 package 跟原 class 的目錄結構保持一致。

4. 版本: 

    * Spring Boot <= 2.1 : 僅能使用 `JUnit4`
    * Spring Boot 2.2 或 2.3 : 可使用 `JUnit4`, `JUnit5`
    * Spring Boot >= 2.4 : 僅能使用 `JUnit5`



5. `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <!-- 限制只能使用junit5的功能 -->
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
        <!--  -->
    </dependency>
    ```

6. 使用 `org.junit.jupiter.api.Test`，加註解在方法上，`class 前加上 public`，建立時預設沒有加。

    ```java
    public class CaculatorTest {

        @Test
        public void add() {//只能用void方法，方法名隨意取
            Caculator caculator = new Caculator();
            int result = caculator.add(1,2);

            assertEquals(3, result);
        }
    }
    ```

7. 常見的 assert 方法

    |方法|通過條件|
    |--|--|
    |assertNull(A)| A 為 null|
    |assertNotNull(A)| A 不為 null|
    |assertEquals(A, B)| A 和 B 相等，使用`equals()`方法判斷|
    |assertTrue(A)| A 為 true|
    |assertFalse(A)|A 為 false|
    |assertThrows(exception, method)|執行 method 時，會拋出此 exception|


<br/>

<br/>

## 測試用註解

`@beforeEach`: 在每個 test case 前都先執行一次這個註解的方法 (常用)。

`@afterEach` : 同上，每個 test case 後執行。

```java
@BeforeEach
public void beforeEach() {
    System.out.println("@beforeEach");
}

@AfterEach
public void afterEach() {
    System.out.println("@afterEach");
}

@Test
public void test1() {
    System.out.println("test1");
}

@Test
public void test2() {
    System.out.println("test2");
}
```
```java
// 輸出
// @beforeEach
// test1
// @afterEach
// @beforeEach
// test2
// @afterEach
```

<br/>

<br/>

`@BeforeAll` : 在執行所有 test case 之前，執行一次這個註解的方法 (常用)。

`@AfterAll` : 同上，執行所有 test case 之後。

使用這兩個註解時，方法必須聲明為 `static`，所以這兩個註解`沒辦法去測試到 Bean`，故不太常用。

```java
@BeforeAll
public static void beforeAll() {
    System.out.println("@beforeAll");
}

@AfterAll
public static void afterAll() {
    System.out.println("@afterAll");
}

@Test
public void test1() {
    System.out.println("test1");
}

@Test
public void test2() {
    System.out.println("test2");
}
```
```java
// 輸出
// @beforeAll
// test1
// test2
// @afterAll
```

<br/>

<br/>

`@Disabled` : 忽略該 @Test 不執行。

```java
@Disabled
public void test() {

}
```

<br/>

<br/>

`@DisplayName` : 自定義顯示名稱，會顯示在左下角 Run 的 tab 中。

```java
@DisplayName("自定義名稱")
public void test() {

}
```
