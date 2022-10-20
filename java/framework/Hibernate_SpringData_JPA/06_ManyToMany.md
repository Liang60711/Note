## ManyToMany 舉例
1. Student 有多個 Course，Course 也有多個 Student。
2. `joinColumns`用來指定mapping表關聯自己的欄位；`inverseJoinColumns`用來指定mapping表關聯對方的欄位。

```java
// Student 這邊不加 constraint
public class Student {
    private Long studentId;
}
```

```java
public class Course {

    private Long courseId;

    @ManyToMany(
        cascade = CascadeType.ALL
    )
    @JoinTable(
        name = "student_course_mapping",    // mapping資料表名稱
        joinColumns = @JoinColumn(          // mapping表的FK名為 course_id ，指向 `course` 的 `course_id` 欄位 (Course類的courseId屬性)
            name = "course_id",
            referencedColumnName = "courseId"
        ),
        inverseJoinColumns = @JoinColumn(   // mapping表的
            name = "student_id",
            referencedColumnName = "studentId"
        )
    )
    private List<Student> students;
}
```