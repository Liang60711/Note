## 設定
`pom.xml`加入dependency
```xml
<!-- jdbc功能 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<!-- mysql driver -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.22</version>
</dependency>
```

`application.properties`
```properties
# 選擇使用的driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 連線的url，後面的query可帶可不帶
spring.datasource.url=jdbc:mysql://localhost:3306/myjdbc?serverTimezone=Asia/Taipei&characterEncoding=utf-8
# mysql 登入資訊
spring.datasource.username=root
spring.datasource.password=root
```
Spring JDBC 會自動創建名為 `NamedParameterJdbcTemplate` 的 Bean，jdbc 的功能都是出自這個bean。

<br/>

<br/>

## update():  方法可執行 `INSERT`、`UPDATE`、`DELETE` 方法。


這邊可以直接注入`NamedParameterJdbcTemplate`的原因是當在`application.properties`檔案中設定sql相關變數後，spring會自動將建立 NamedParameterJdbcTemplate 這個 bean。


1. `insert` 和 `delete`

    ```java
    @RestController
    public class StudentController {

        @Autowired
        private NamedParameterJdbcTemplate namedParameterJdbcTemplate;

        /**
         * INSERT
         */
        @PostMapping("/students")
        public String insert(@RequestBody Student student) {
            // 冒號為佔位符
            String sql = "INSERT INTO student(id, name) VALUE (:studentId, :studentName)";
            // 將變數丟給map
            Map<String, Object> map = new HashMap<>();
            map.put("studentId", student.getId());
            map.put("studentName", student.getName());

            namedParameterJdbcTemplate.update(sql, map);

            return "insert sql";
        }

        /**
         * DELETE
         */
        @DeleteMapping("/students/{sid}")
        public String delete(@PathVariable Integer sid) {
            String sql = "DELETE FROM student WHERE id = :studentId";

            Map<String, Object> map = new HashMap<>();
            map.put("studentId", sid);
            namedParameterJdbcTemplate.update(sql, map);

            return "delete sql";
        }
    }
    ```

2. 取下筆自動產生的 `id` 值 (id設為AUTO_INCREMENT)

    ```java
    @PostMapping("/students")
    public String insert(@RequestBody Student student) {

        String sql = "INSERT INTO student(name) VALUE (:studentName)";
        Map<String, Object> map = new HashMap<>();
        map.put("studentName", student.getName());

        // 1. 先產生一個 keyHolder
        KeyHolder keyHolder = new GeneratedKeyHolder();

        // 2. update()方法會去更新keyHolder參數的值
        namedParameterJdbcTemplate.update(sql, new MapSqlParameterSource(map), keyHolder);

        // 3. getKey取值
        int id = keyHolder.getKey().intValue();

        return "insert sql";
    }
    ```

3. `batchUpdate()`，實作批次插入，`MapSqlParameterSource`可參考

    > https://matthung0807.blogspot.com/2020/04/spring-jdbc-mapsqlparametersource.html

    ```java
    @PostMapping("/students/batch")
        public String insertList(@RequestBody List<Student> studentList) {
            String sql = "INSERT INTO student(name) VALUE (:studentName)";

            // 1.建立一個跟studentList長度相同的陣列
            MapSqlParameterSource[] parameterSources = new MapSqlParameterSource[studentList.size()];

            // 2. 建立物件陣列
            for(int i=0; i<studentList.size(); i++){
                Student student = studentList.get(i);

                parameterSources[i] = new MapSqlParameterSource();
                parameterSources[i].addValue("studentName", student.getName());
            }

            // 3.批次插入
            namedParameterJdbcTemplate.batchUpdate(sql, parameterSources);
            return "insertList";
        }
    ```


<br/>

<br/>

## query() / RowMapper
* 語法: `query(String sql, Map<String, Object> map, RowMapper<T> rowMapper)`，第三個參數需將sql查詢出的數據使用 `RowMapper` 或 `ResultSetExtractor` 轉換成 java object。

* `RowMapper` 和 `ResultSetExtractor` 的差別: 

    1. `RowMapper` : 一次只能取得一筆row，無法取得到其他row資訊。
    2. `ResultSetExtractor` : 一次可以取得到所有row的資訊，可以自由組成不同數據，但不太常用。

1. `query()` 取全部列表； url = `/students`

    ```java
    /**
     * Controller
     * SELECT 所有的數據
     */
    @GetMapping("/students")
    public List<Student> selectAll() {//返回值為List<Student>，代表回傳給前端一包json，裡面是Student物件的陣列
        // 1. sql語句不使用 * 來選取欄位，會使效能降低
        String sql = "select id, name from student";
        // 2. 空map
        Map<String, Object> map = new HashMap<>();
        // 3. 實作出一個Student的RowMapper
        List<Student> list = namedParameterJdbcTemplate.query(sql, map, new StudentRowMapper());

        return list;
    }
    ```
    ```java
    /**
     * RowMapper
     * 使用 RowMapper interface 實作出 StudentRowMapper
     */
    public class StudentRowMapper implements RowMapper<Student> {

        @Override
        public Student mapRow(ResultSet resultSet, int i) throws SQLException {//resultSet是從sql查詢出的數據
            Student student = new Student();
            student.setId(resultSet.getInt("id"));//resultSet中取id值
            student.setName(resultSet.getString("name"));

            return student;
        }
    }
    ```
    ```java
    /**
     * ResaultSetExtractor
     * 使用 ResaultSetExtractor interface 實作出 StudentResultSetExtractor
     */
    public class StudentResultSetExtractor implements ResultSetExtractor {

        @Override
        public List<Student> extractData(ResultSet resultSet) throws SQLException, DataAccessException {
            
            List<Student> result = new ArrayList<>();
            
            while(resultSet.next()){
                Student student = new Student();
                student.setId(resultSet.getInt("id"));
                student.setName(resultSet.getString("name"));
                
                result.add(student);
            }
            
            return result;
        }
    }
    ```


2. `query` 取特定id； url = `/students/{studentId}`

    ```java
    @GetMapping("/students/{studentId}")
    public Student getStudents(@PathVariable Integer studentId){
        String sql = "SELECT id, name from student WHERE id = :studentId";
        Map<String, Object> map = new HashMap<>();
        map.put("studentId", studentId);

        RowMapper<Student> rowMapper = new StudentRowMapper();

        List<Student> list = namedParameterJdbcTemplate.query(sql, map, rowMapper);

        // 檢查是否有撈到資料
        if (list.size() > 0) {
            return list.get(0);
        }
        return null;
          
    }
    ```

<br/>

<br/>


|SQL欄位類型|對應到RowMapper要用什麼方法取|對應的Java類型
|--|--|--|
|INT|getInt()|Integer|
|VARCHAR|getString()|String|
|BIGINT|getLong()|Long|
|DOUBLE|getDouble()|Double|
|FLOAT|getFloat()|Float|
|BOOLEAN|getBoolean()|boolean|
|TIMESTAMP|`getTimestamp()`|`Date`|


<br/>

<br/>

## Transactional 交易
1. 用法: 加在 class 或方法上，通常是`方法`。
2. @Transactional 在 `main` 資料夾中

    * 程式運行`中途發生錯誤`的話，才會 rollback 已經執行的db操作，將數據恢復。


3. @Transactional 在 `test` 資料夾中

    * 在單元測試結束後，`強制 rollback` 所有對db的操作，將數據恢復。



<br/>

<br/>

## 多個資料庫連線
``application.properties``設定
```properties
# test1 db
spring.datasource.test1.driver-class-name=com.mysql.cj.jdbc.Driver
# 多資料庫jdbc-url，有別於單資料庫url
spring.datasource.test1.jdbc-url=jdbc:mysql://localhost:3306/test1?serverTimezone=Asia/Taipei&characterEncoding=utf-8
spring.datasource.test1.username=root
spring.datasource.test1.password=root

# test2 db
spring.datasource.test2.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.test2.jdbc-url=jdbc:mysql://localhost:3306/test1?serverTimezone=Asia/Taipei&characterEncoding=utf-8
spring.datasource.test2.username=root
spring.datasource.test2.password=root
```
自訂配置檔
```java
@Configuration //這個不要忘記
public class DataSourceConfiguration {
    /**
     * 連線至 test1 的 DataSource 和 NameParameterJdbcTemptlate
     */
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.test1")
    public DataSource test1DataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    public NamedParameterJdbcTemplate test1JdbcTemplate(@Qualifier("test1DataSource") DataSource dataSource) {
        return new NamedParameterJdbcTemplate(dataSource);
    }

    /**
     * 連線至 test2 的 DataSource 和 NameParameterJdbcTemptlate
     */
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.test2")
    public DataSource test2DataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    public NamedParameterJdbcTemplate test2JdbcTemplate(@Qualifier("test2DataSource") DataSource dataSource) {
        return new NamedParameterJdbcTemplate(dataSource);
    }
}
```
在 Controller 中使用

```java
@RestController
public class StudentController {
    // 分別注入兩個 NamedParameterJdbcTemplate
    @Autowired
    @Qualifier("test1JdbcTemplate")
    private NamedParameterJdbcTemplate test1JdbcTemplate;

    @Autowired
    @Qualifier("test2JdbcTemplate")
    private NamedParameterJdbcTemplate test2JdbcTemplate;

}
```