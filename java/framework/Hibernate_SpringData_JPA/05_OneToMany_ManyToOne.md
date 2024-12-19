## 舉例
* 舉例，一個`客戶`有多個`聯繫人`，多個`聯繫人`屬於一個`客戶`
1. 建立兩個實體類。
2. 讓兩個實體類中互相建立關係。 
    * 在客戶中表示多個聯繫人，需使用 `set` 集合。
    * 在聯繫人中表示屬於哪個客戶。


<br/>

<br/>

<br/>

<br/>

## @OneToMany 參數 FetchType.LAZY 和 FetchType.EAGER 差別
* `FetchType.LAZY` 在用到時才載入關聯的物件。
* `FetchType.EAGER` 在查詢時立刻載入關聯的物件。

<br/>

<br/>

## 舉例
一個 Teacher，有許多 Course
```java
@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Teacher {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long teacherId;
    private String firstName;
    private String lastName;

    @OneToMany(
        fetch = FetchType.LAZY,
        mappedBy = "teacher"
    )
    private List<Course> courseList = new ArrayList<>();
}
```
```java
@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Course {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long courseId;

    @ManyToOne(cascade = CascadeType.ALL)
    @JoinColumn(
        name = "teacher_id",
        referencedColumnName = "teacherId"  // teacherId 沒寫 @Column(name = "teacher_id")，故這邊寫 teacherId
    )
    private Teacher teacher;
}
```

<br/>

<br/>

## 坑

當使用 @OneToMany 和 @ManyToOne 時，有時會產生無限遞迴問題，造成 StackOverFlowError。

> https://stackoverflow.com/questions/17445657/hibernate-onetomany-java-lang-stackoverflowerror


解決方式可以使用 `@JsonManagedReference` 和 `@JsonBackReference`

```java
// group
@JsonManagedReference
@OneToMany(mappedBy = "group")
private List<Device> devices;

// device
@JsonBackReference
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "group_id", referencedColumnName = "id", insertable = false, updatable = false)
private group group;
```