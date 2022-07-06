## 常用註解

`@SpringBootTest` :  
只要加上在測試的 class 上此註解，運行單元測試時，Spring Boot，
1. 會去啟動 Spring 容器，創建所有的 bean。
2. 也會去啟動所有的 `@Configuration`，相當於直接運行 Spring Boot 程式。

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

        // 2. mockMvc.perform 去執行剛建立的 request
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

