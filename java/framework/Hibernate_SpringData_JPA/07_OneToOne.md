## 舉例
1. course 和 courseMaterial 為`一對一關係`。

2. 有外鍵的實體使用 `@OneToOne` 和 `@JoinColumn` 註解該屬性，

3. 沒有外鍵的實體使用 `@OneToOne(mappedBy="")`，指定[關聯實體]的屬性(即外鍵)，若不寫 `mappedBy` 屬性，則資料庫會產生第3張表(pivot table)來記錄這兩張表的關係。

```java
// Course

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@Table
@Entity
public class Course {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long courceId;
    private String title;
    private Integer credit;

    @OneToOne(mappedBy = "course")  // 指向CourseMaterial的course成員變數(即擁有方的外鍵屬性名)
    private CourseMaterial courseMaterial;
}
```
```java
// CourseMaterial

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@Table
@Entity
public class CourseMaterial {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long courseMaterialId;
    private String url;

    @OneToOne(
            cascade = CascadeType.ALL,  // 聯級操作
            fetch = FetchType.LAZY
    )
    @JoinColumn(
            name = "course_id",     // 在coursematerial表建立一個名為course_id的欄位
            referencedColumnName = "course_id"   // 此欄位的值為course表的course_id欄位 
    )
    private Course course;
}
```

<br/>

<br/>

## CascadeType 與 FetchType
> https://openhome.cc/Gossip/EJB3Gossip/CascadeTypeFetchType.html