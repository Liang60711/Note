## 靜態方法紀錄資訊
Logger import 目錄 `import org.slf4j.Logger`
```java
@RestController
public class StudentController {

    // 制式化寫法
    private final static Logger log = LoggerFactory.getLogger(StudentController.class);

    @Autowired
    private StudentService studentService;

    @GetMapping("/student/{studentId}")
    public ResponseEntity<Student> read(@PathVariable Integer studentId) {

        // 使用 log 物件印出
        // 使用大括號填入變數
        log.info("取得 student {}", studentId);
        log.warn();
        log.error();

        Student student = studentService.getById(studentId);

        return ResponseEntity.status(HttpStatus).body(student);
    }
}
```
在字串中加入大括號 { }，可放入多個變數
```java
log.warn("Email: {} are registered by {} !", userRegisterRequest.getEmail(), userRegisterRequest.getUser());
```