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
            cascade = CascadeType.ALL
    )
    @JoinColumn
    private List<Course> courseList;
}
```
