## 測試檔案位置
在 springboot 中，`測試類`和`待測試類`需要在相同的路徑下，否則需要指定路徑。

舉例: 待測試類在 `com.test` 路徑下，測試類在 `com` 路徑下，測試類就要指定待測試類的路徑
。

```java
// 加上 classes 屬性
@SpringBootTest(classes = UserController.class)
class UserControllerTests {

}
```


<br/>

<br/>

## 常用註解

`@SpringBootTest` :  
只要加上在測試的 class 上此註解，運行單元測試時，Spring Boot，
1. 會去啟動 Spring 容器，創建所有的 bean。
2. 也會去啟動所有的 `@Configuration`，相當於直接運行 Spring Boot 程式。
3. 此註解中可以額外添加臨時屬性，可以蓋過 application.yml 中的屬性層級。

    ```java
    // 優先級: 方法2 > 方法1 > yml配置
    // 方法1
    @SpringBootTest(properties = "server.port=8080")
    // 方法2
    @SpringBootTest(args = {"--server.port=8080"})
    public class SecurityApplicationTest {

    }
    ```


<br/>

<br/>

## Dao層測試

crud範例

```java
@SpringBootTest // 建立bean, 載入configuration
public class StudentDaoImplTest {

    @Autowired
    private StudentDao studentDao;

    @Test
    public void getById() {
        Student student = studentDao.getById(1);
        assertNotNull(student);
        assertEquals("Amy", student.getName());
        assertEquals(90.3, student.getScore());
        assertTrue(student.isGraduate());
        assertNotNull(student.getCreateDate());
    }

    @Test
    @Transactional //測試完會rollback
    public void deleteById() {
        studentDao.deleteById(3);

        Student student = studentDao.getById(3);
        assertNull(student);
    }

    @Test
    @Transactional
    public void insert() {
        Student student = new Student();
        student.setName("Kevin");
        student.setScore(66.2);
        student.setGraduate(true);

        Integer studentId = studentDao.insert(student);

        Student result = studentDao.getById(studentId);
        assertNotNull(result);
        assertEquals("Kevin", result.getName());
        assertEquals(66.2, result.getScore());
        assertTrue(result.isGraduate());
        assertNotNull(result.getCreateDate());
    }

    @Test
    @Transactional
    public void update() {
        Student student = studentDao.getById(3);
        student.setName("John");

        studentDao.update(student);

        Student result = studentDao.getById(3);
        assertNotNull(result);
        assertEquals("John", result.getName());
    }
}
```

<br/>

<br/>

## Controller層測試
Controller層，需要透過模擬真實的API call來測試，可以透過 `MockMvc` 的功能模擬真實的 API call。



`@AutoConfigureMockMvc` 須加在 class。
```java
@SpringBootTest
@AutoConfigureMockMvc  // 1. 加上註解
public class StudentControllerTest {
    
    @Autowired  // 2.注入MockMvc
    private MockMvc mockMvc;


    @Test
    public void getById() throws Exception {
        // 1. 建立一個 mock request
        RequestBuilder requestBuilder = MockMvcRequestBuilders.get("/students/3");

        // 2. mockMvc.perform 發起剛建立的 request
        // 3. 執行後驗證
        mockMvc.perform(requestBuilder).andExpect(MockMvcResultMatchers.status().is(200));
    }
}
```

<br/>

<br/>

`RequestBuilder` : 建立一個自定義的 Request，用來做測試。

get() 的第二參數為可變參數，帶入url裡面的`{}`變數。
```java
// GET request
RequestBuilder requestBuilder = MockMvcRequestBuilders
        .get("/students/{studentId}", 3)
        .header("headerName", "headerValue")
        .param("key", "value");
```
```java
// POST request
RequestBuilder requestBuilder = MockMvcRequestBuilders
        .post("/students")
        .contentType(MediaType.APPLICATION_JSON) // 這行記得帶
        .content("{\n" +
                "  \"name\": \"Hank\",\n" +
                "  \"score\": 99,\n" +
                "  \"graduate\": false\n" +
                "}");
```

<br/>

<br/>

`MockMvc` 架構包含3部分
1. 建立 RequestBuilder，建立路由、參數、header。

2. 執行 mockMvc.perform()，

3. 執行後方法
    
    * `andDo()` : 輸出結果
    * `andExcept()` : 驗證結果
    * `andReturn()` : 取得結果

* `andExpect` + `jsonPath()` : 驗證 json

    > https://jsonpath.com/

    ```java
    // 測試 GET 回傳結果
    mockMvc.perform(requestBuilder) // 執行request，並取回http response
            .andDo(print()) // 輸出回傳body結果，比較好寫
            .andExpect(status().isOk()) // status()是靜態方法
            .andExpect(jsonPath("$.id", equalTo(3)))    // 驗證json資料，$字號代表整包json物件
            .andExpect(jsonPath("$.name"), notNullValue());

    ```
    ```java
    // 測試 POST 回傳結果
    mockMvc.perform(requestBuilder)
            .andExpect(status().is(201)); // 201 Created
    ```

<br/>

<br/>


* `andReturn()` 取得結果，用 MvcResult 接變數。
    
    ```java
    MvcResult mvcResult = mockMvc.perform(requestBuilder)
            .andDo(print())
            .andExpect(status().is(200))
            .andExpect(jsonPath("$.id", equalTo(3)))
            .andExpect(jsonPath("$.name", notNullValue()))
            .andReturn();
    
    String body = mvcResult.getResponse().getContentAsString();
    ```

<br/>

<br/>

## Mock 測試
1. 目的: 建立一個假的 bean，去替換掉 Spring容器中原有的 bean，以達到單元測試必須各自獨立的規則，總之，就是為了`隔絕外界的變因`。
2. 舉例: 
    * 依賴關係: `Controller >> Service >> Dao`
    * 測試 Service 層時，使用 mock 建立一個假的 Dao，避免 Dao 測試失敗，拖累 Service 的程式碼。

<br/>

<br/>

## Mockito 測試工具
1. 功能: 
    * 模擬方法的返回值。
    * 模擬拋出 Exception
    * 紀錄方法的使用次數、順序。

2. 寫法:

    * `@MockBean` : 產生一個假的bean，替換掉Spring容器中的bean，可以自定義假方法；沒有定義的方法，預設都返回 `null`。
    * `@SpyBean` : 不產生假的bean，使用正常Spring容器的bean，可以自定義假方法來替換；沒有定義的方法，預設使用真實的方法。
    * `Mockito.when().thenReturn()` : 模擬測試時的條件，並自定義返回值。
    * `Mockito.when().thenThrow()` : 模擬測試時的條件，並拋出 Exception。

3. 限制: 
    * 不能 mock `static` 方法
    * 不能 mock `private` 方法
    * 不能 mock `final class`

4. 舉例: 

    ```java
    @SpringBootTest //1.建立bean
    public class StudentServiceImplMockTest {

        //2.注入要測試的類
        @Autowired
        private StudentService studentService;

        //3.產生一個假的bean，替換掉Spring容器中的bean
        @MockBean
        private StudentDao studentDao;

        @Test
        public void getById(){
            //4-1. 建立一個模擬的返回值
            Student mockStudent = new Student();
            mockStudent.setId(100);
            mockStudent.setName("I am mock");

            //4-2. 寫條件
            Mockito.when(studentDao.getById(Mockito.any())).thenReturn(mockStudent);//Mockito.any() 為任何參數
            Mockito.when(studentDao.insert(Mockito.any())).thenThrow(new RuntimeException());
            Mockito.verify(studentDao, Mockito.times(2)).getById(Mockito.any());//紀錄方法的使用次數

            //4-3. 寫測試
            Student student = studentService.getById(3);
            assertNotNull(student);
            assertEquals(100, student.getId());
            assertEquals("I am mock", student.getName());
        }
    }
    ```

<br/>

<br/>

## H2資料庫
1. 目的: 為了提升單元測試的穩定性，不會受到外部資料庫的影響，可以在 Spring Boot 啟動時被生成，運行結束時銷毀。
2. 是一種嵌入式資料庫，常用在單元測試，降低程式對實體資料庫的依賴。

3. 設定: 
    * `pom.xml` 添加 dependency

        > https://mvnrepository.com/artifact/com.h2database/h2
    
    * 建立設定檔 `test\resources\application.properties`，跑測試都會使用這個設定檔

    * 若 `main\resources\application.properties` 有添加其他設定，也要全部複製到測試的設定檔，`兩個設定檔各自獨立，內容不互通`。

    
        ```properties
        # H2 db 設定資訊
        spring.datasource.driver-class-name=org.h2.Driver
        spring.datasource.url=jdbc:h2:mem:testdb
        spring.datasource.username=sa
        spring.datasource.password=sa

        # 使用jpa時，避免自動根據 @Entity類自動建表
        spring.jpa.hibernate.ddl-auto=none
        ```

4. 建立 `schema.sql` 和 `data.sql`，若 h2資料庫已連線，會檢查是否有此二檔案，有的話就自動載入。

    ```
    resources
        ├─ application.properties
        ├─ data.sql
        └─ schema.sql
    ```
    
    ```sql
    -- schema.sql
    -- 記得加 IF NOT EXISTS
    CREATE TABLE IF NOT EXISTS student (
            id INT PRIMARY KEY AUTO_INCREMENT,
            name VARCHAR(30),
            score DOUBLE,
            graduate BOOLEAN,
            create_date TIMESTAMP
    );
    ```

    ```sql
    -- data.sql
    INSERT INTO student (name, score, graduate, create_date) VALUES ('Amy', 90.3, true, '2021-09-01 10:20:33');
    INSERT INTO student (name, score, graduate, create_date) VALUES ('Rom', 34.6, false, '2021-08-10 17:21:14');
    INSERT INTO student (name, score, graduate, create_date) VALUES ('Judy', 100.0, true, '2021-09-05 12:19:48');
    INSERT INTO student (name, score, graduate, create_date) VALUES ('Mike', 87.2, true, '2021-09-03 15:01:15');
    ```