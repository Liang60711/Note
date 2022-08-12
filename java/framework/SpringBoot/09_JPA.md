## JPA 
1. Java Persistence API，JPA 不是一種框架，而是一個Sun官方提出的`ORM標準規範`。
2. 優點: 由於已經使用 java 物件來映射到 sql 的資料表，可以隨意的切換資料庫種類，不用擔心個資料庫語法不同而去修改程式。
3. 缺點: JPA 底層還是由 JDBC 來執行，故效能會比較差。

設定 `pom.xml`
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.22</version>
</dependency>
```
設定 `application.properties`，和 jdbc 設定相同
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/myjpa?serverTimezone=Asia/Taipei&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=root

# 在 intelliJ console 顯示 sql 語法
spring.jpa.show-sql=true
```

<br/>

<br/>


## save(), findById(), deleteById() 
1. 用法: update or create，找不到id更新就會新建立資料。
2. 註解用法: 


    |Jpa註解|用法|
    |--|--|
    |@Entity|宣告為數據模型層的Bean|
    |@Table|對應到資料庫中的Table名稱|
    |@Column|對應到 Table 的欄位中的欄位名稱|
    |@Id|對應 Table 的 Primary Key|
    |@GeneratedValue|設定此 Column 的生成方式|


<br/>

<br/>

3. 範例，建立 Restful api


    ```java
    @RestController
    public class StudentController {

        // 1. 注入 Repository
        @Autowired
        private StudentRepository studentRepository;

        @PostMapping("/students")
        public String insert(@RequestBody Student student) {

            studentRepository.save(student);

            return "insert method";
        }
        // 2.GET
        @GetMapping("/students/{studentId}")
        public Student select(@PathVariable Integer studentId){
            // findById()是回傳一個Optional，所以用orElse()
            Student student = studentRepository.findById(studentId).orElse(null);
            return student;
        }

        // 3. PUT
        @PutMapping("/students/{studentId}")
        public String update(@PathVariable Integer studentId, @RequestBody Student student){
            
            Student s = studentRepository.findById(studentId).orElse(null);

            // 先判斷是否有這筆數據
            if (s != null) {
                student.setId(studentId);
                studentRepository.save(student);
                return "update method";
            }

            return "update failed";
        }

        // 4. DELETE
        @DeleteMapping("/students/{studentId}")
        public String delete(@PathVariable Integer studentId){

            studentRepository.deleteById(studentId);
            return "delete method";
        }
    }
    ```
    Student.class
    ```java
    @Entity
    @Table(name = "student")
    public class Student {

        @Id
        // 使用 IDENTITY 為 "id BIGINT NOT NULL AUTO_INCREMENT"
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        @Column(name = "id")
        Integer id;

        @Column(name = "name")
        String name;

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }

    ```
    StudentRepository
    ```java
    // 泛型內 <T, ID>
    public interface StudentRepository extends CrudRepository<Student, Integer> {

    }
    ```

<br/>

<br/>

## @Query 執行原生SQL語法
1. 目的: findByXxx() 無法寫出複雜的查詢邏輯，故有原生SQL可以使用。
2. 語法: `@Query(value, nativeQuery)`，
    
    * value 中的 `?1` 代表方法中第一個傳進的參數
    * nativeQuery = true，原生 SQL 語法
    * nativeQuery = false，為 JPQL 語法

3. 舉例: 

    ```java
    public interface StudentRepository extends CrudRepository<Student, Integer> {

        @Query(value = "SELECT id, name FROM student WHERE id = ?1 AND name = ?2", nativeQuery = true)
        Student test1(Integer id, String name);
    }
    ```

