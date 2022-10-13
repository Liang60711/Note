## 舉例
* 舉例，一個`客戶`有多個`聯繫人`，多個`聯繫人`屬於一個`客戶`
1. 建立兩個實體類。
2. 讓兩個實體類中互相建立關係。 
    * 在客戶中表示多個聯繫人，需使用 `set` 集合。
    * 在聯繫人中表示屬於哪個客戶。

3. 標示註解。
    * `@Entity`: 註解為實體類，此類必須要有一個無參數的constructor。
    * `@GeneratedValue`: 指定 ID 的生成方式。
    * `@Transient`: 不會把此屬性映射到資料表
    * `@OneToMany`: 描述一對多的關聯，該屬性應該爲集合類型，在資料表中並沒有實際欄位. 
    * `@Embedded` / `@Embeddable`: 將一個類嵌入到此實體類中。


<br/>

<br/>

## 實體類
舉例，一個客戶有多個聯繫人，多個聯繫人屬於一個客戶
```java
@Entity    
@Table(name = "customer")
@Data // lombok
class Customer {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)// 自動增長
    @Column(name = "id")
    private Integer cid;
    @Column(name = "name")
    private String cName;
    @Column(name = "level")
    private String cLevel;
    @Column(name = "mobile")
    private String cMobile;
    @Embedded   // 可以將 Address 類嵌入到此實體，
    private Address address;

    // 建立關係: 在客戶中表示多個聯繫人
    @OneToMany(fetch = FetchType, cascade = CascadeType) 
    private Set<LinkMan> LinkManSet = new HashSet<>();
}
```
```java
@Entity
@Table(name = "linkman")
@Data // lombok
class LinkMan {
    private Integer lid;
    private String lName;
    private String lGender;
    private String lPhone;

    // 建立關係: 在聯繫人中表示屬於哪個客戶
    private Customer customer;
}
```
```java
// 被嵌入的類要加上 @Embeddable
// 可以不是實體類
@Embeddable
public class Address {
    
    @NotNull
    @Size(max = 100)
    private String addressLine1;

    @NotNull
    @Size(max = 100)
    private String addressLine2;
}
```

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
