## 連線配置檔
```yml
spring:
  jpa:
    hibernate:
      ddl-auto: update  # 使用entity更新資料表
    show-sql: true      # 是否顯示SQL (logback.xml若有也要設為 level=DEBUG)
    
  datasource:
    url: jdbc:mysql://localhost:3306/myjpa
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver
```

<br/>

ddl-auto 的選擇

|||
|--|--|
|create-drop|啟動時建立Table，當次Session關閉則刪除，嵌入式資料庫(H2)的預設值|
|create|啟動時建立Table，下次啟動會覆蓋上次的，會造成資料遺失|
|update|若無Table則啟動時建立, 若有則視有無變更自動Update，`欄位建立了就無法刪除`|
|none|預設值，什麼都不做|


<br/>

<br/>

<br/>

<br/>

## Hibernate

1. 應用在 DDD 架構中的 dao 層。
2. 對資料庫進行 crud，底層代碼為 jdbc，封裝成 ORM 框架。

<br/>

<br/>

## 基本設定流程 (dtd約束)
1. pom.xml 檔案加入依賴。
2. 建立 entity(package)，並建立實體類 `User`。
3. 在實體類旁建立配置文件(映射文件)，名為 `User.hbm.xml`，若使用 Maven，此檔案需要放在 `src/main/resources`。

    ```java
    /**
     * Entity類
     */
    public class User {

        private Integer uid;
        private String username;
        private String password;
        private String address;

        // getter and setter
    }
    ```

    ```xml
    <!-- 配置文件 -->
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE hibernate-mapping PUBLIC
            "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
            "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">


    <hibernate-mapping>
        <!--  1. 配置類和資料表映射  -->
        <!--  class 標籤:
                name屬性: entity類的完整路徑
                table屬性: 資料表名稱
        -->
        <class name="org.example.entity.User" table="student">
            <!--  2. 配置entity類id和資料表id對應
                    hibernate要求entity類有一個屬性唯一值
                    hibernate要求資料表有column作為唯一值
            -->
            <!--  id標籤:
                    name屬性: entity類的id屬性名稱
                    columns屬性: 生成的資料表欄位名稱
            -->
            <id name = "uid" column="uid">
                <!--  設置id生成策略
                        native: 生成資料表id值就是主鍵自動增長
                -->
                <generator class="native"></generator>
            </id>
            <!--  3.配置其他屬性和資料表欄位映射  -->
            <property name="username" column="username"></property>
            <property name="password" column="password"></property>
            <property name="address" column="address"></property>
        </class>
    </hibernate-mapping>
    ```

4. 建立 hibernate 核心配置文件 (檔名固定叫做`hibernate.cfg.xml`)，須放在 src目錄下； hibernate只會載入核心配置文件，不會載入映射文件，所以需要在此檔案中添加映射文件

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!DOCTYPE hibernate-configuration PUBLIC
            "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
            "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

    <hibernate-configuration>

        <session-factory>
            <!--  1. 配置資料庫信息(可選填，這邊其實可以在 properties/yml 中設定)  -->
            <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
            <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernate</property>
            <property name="hibernate.connection.username">root</property>
            <property name="hibernate.connection.password">root</property>

            <!--  2. 配置hibernate信息(可選填，這邊其實可以在 properties/yml 中設定)  -->
            <!-- 顯示實際操作資料庫時的SQL語句 -->
            <property name="show_sql">true</property>
            <!-- 將顯示的SQL排版，方便觀看 -->
            <property name="format_sql">true</property>
            <!--  hibernate會自動建立資料表，需要配置之後
                        update: 如果已經有表，會更新；如果沒有會建立表
            -->
            <property name="hbm2ddl.auto">update</property>
            <!-- 配置 SQL 方言，舉例像是 limit 關鍵字只能在 mysql 中使用，所以需億澳做額外方言設定 -->
            <property name="dialect">org.hibernate.dialect.MySQL5Dialect</property>

            <!--  3. 把映射文件放到核心配置文件中  -->
            <!-- 舊寫法: 使用xml文件 -->
            <mapping resource="org/example/entity/User.hbm.xml" />
            <!-- 註解寫法: 使用Entity class -->
            <mapping class="com.example.jsp.entity.Student"></mapping>

        </session-factory>

    </hibernate-configuration>
    ```


<br/>

<br/>


## 實現添加操作

1. 載入 hibernate 核心配置文件。
2. 創建 SessionFactory 物件。
3. 使用 SessionFactory 建立 Session 物件。
4. 開啟transaction。
5. 寫具體 CRUD 操作。
6. 提交transaction。
7. 關閉資源。

```java
// 1. 載入 hibernate 核心配置文件。
// 到src目錄下找到名稱為 hibernate.cfg.xml 放到 cfg 物件中
Configuration cfg = new Configuration();
cfg.configure();

// 2. 創建 SessionFactory 物件。
// 讀取核心文件內容，創建 SessionFactory 物件後，根據核心文件中的映射關係，在資料庫中把資料表創建
SessionFactory sessionFactory = cfg.buildSessionFactory();

// 3. 使用 SessionFactory 建立 Session 物件。
// 建立資料庫連線
Session session = sessionFactory.openSession();

// 4. 開啟transaction。
Transaction tx = session.beginTransaction();
// 5. 寫具體 CRUD 操作。
User user = new User();
user.setUsername("小王");
user.setPassword("250");
user.setAddress("japan");
session.save(user);

// 6. 提交transaction。
tx.commit();

// 7. 關閉資源。
session.close();
sessionFactory.close();
```

<br/>

<br/>

## HibernateConfig 配置建立 sessionFactory 的 Bean
```java
@Configuration
@EnableTransactionManagement
public class HibernateConfig {
    
    /**
     * 設定sessionFactory bean
     */
    @Bean
    public LocalSessionFactoryBean sessionFactory() {
        LocalSessionFactoryBean sessionFactory = new LocalSessionFactoryBean();
        sessionFactory.setDataSource(dataSource());     // 連線資訊
        sessionFactory.setPackagesToScan(
          {"com.baeldung.hibernate.bootstrap.model"}    // entity位置
        );
        sessionFactory.setHibernateProperties(hibernateProperties());  // 設定連線屬性

        return sessionFactory;
    }

    /**
     * 設定dataSource bean
     */
    @Bean
    public DataSource dataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName("org.postgresql.Driver");
        dataSource.setUrl("jdbc:postgresql://127.0.0.1:5432/testdb");
        dataSource.setUsername("sa");
        dataSource.setPassword("sa");

        return dataSource;
    }

    /**
     * 設定hibernateTransactionManager bean
     * 作用: 管理 transaction
     */
    @Bean
    public PlatformTransactionManager hibernateTransactionManager() {
        HibernateTransactionManager transactionManager = new HibernateTransactionManager();
        transactionManager.setSessionFactory(sessionFactory().getObject());
        return transactionManager;
    }

    private final Properties hibernateProperties() {
        Properties hibernateProperties = new Properties();
        hibernateProperties.setProperty("hibernate.hbm2ddl.auto", "create-drop");
        hibernateProperties.setProperty("hibernate.dialect", "org.hibernate.dialect.H2Dialect");

        return hibernateProperties;
    }
}

```

<br/>

<br/>

## Dao簡易寫法
```java
class TestDao {
    // 1.先注入factory
    private SessionFactory sessionFactory;

    // 2.constructor
    public TestDao(SessionFactory sessionFactory) {
        this.sessionFactory = sessionFactory;
    }

    // 3.寫方法(建立session)
    public void findById(User user) {
        // 取得Session
        Session session = sessionFactory.openSession();
        try {
            // 查找id=1的User
            User user = (User) session.get(User.class, "1");// 回傳是Object類
            // 開啟交易
            Transaction tx= session.beginTransaction();
            // 直接儲存物件
            session.save(user); 
            // 送出交易
            tx.commit();
        } catch(Exception e) {
            tx.rollback();
            e.printStackTrace;
        }finally {
            session.close(); 
        }
    }
}
```

<br/>

<br/>

## Hibernate 的 API 使用
Hibernate 提供了三種物件進行資料查詢，都可以達到查詢資料的目的: 
1. Query
2. Criteria
3. SQLQuery

<br/>

<br/>

## Hibernate 物件的生命週期 (非JPA物件)
Hibernate中的`實體物件`可以分為三種狀態：Persistent、Detached、Transient，注意這邊指的是實體物件。

* Persistent : 

    1. 當物件與資料庫中的資料有對應關係，並且與 Session 實例有關聯而Session 實例尚未關閉（close），則就是在Persistent狀態。
    
    2. 如果將 Transient 狀態的物件使用 Session 的 save() 方法儲存，或是使用 Hibernate 從資料庫載入資料並封裝為物件（例如使用get()、load()等查詢物件的相關方法），則該物件為 Persistent 狀態。

    3. Persistent 狀態的物件對應於資料庫中的一筆資料，物件的id值與資料的主鍵值相同。如果 Session 實例尚未失效，在這期間對物件的任何狀態變動，在 Session 實例關閉（close）或 Transaction 實例執行commit()之後，資料庫中對應的資料也會跟著更新。

    4. 如果將 Session 實例關閉（close），則 Persistent 狀態的物件會成為 Detached 狀態。

    5. 如果使用 Session 的實例 delete() 方法刪除資料，Persistent 狀態的物件由於失去了對應的資料，則它會成為 Transient 狀態。

* Detached

    1. Detached 狀態的物件，其id與資料庫的主鍵值對應，但脫離 Session 實例的管理，例如在使用 load() 方法查詢到資料並封裝為物件之後，將 Session 實例關閉，則物件由 Persistent 變為 Detached，Detached 狀態的物件之任何屬性變動，不會對資料庫中的資料造成任何的影響。

    2. Detached 狀態的物件可以使用 update() 方法使之與資料庫中的對應資料再度發生關聯，此時 Detached狀態的物件會變為 Persistent 狀態。

    3. Detached 物件，若不再被使用，則在適當的時候將被垃圾回收。

    4. Detached 和 Persistent 差別在於 Session 實例是否關閉/開啟，Detached 可以重新連接 Session 變回 Persistent 狀態。
    (A detached instance is an object that has been persistent, but its Session has been closed)

* Transient : 
    
    當直接使用 new 創建出物件，在還沒有使用 save() 之前都是 Transient 物件，這些物件還沒有與資料庫發生任何的關係，不對應於資料庫中的任一筆資料。


* 其他補充 : 
    
    1. Transient 與 Detached 狀態的物件是`非管理狀態`，而 Persistent 狀態的物件是 `管理狀態`， 又稱為Persistent Object，與一個`Persistence Context` 關聯。

    2. 在物件為 Persistent 時，如果物件的屬性發生變化，並且尚未提交之前，物件所攜帶的資料稱之為 Dirty Data，Hibernate會在 Persistence Context 維護物件的最近讀取版本，並在資料提交時檢查兩個版本的屬性是否有變化，如果有的話，則將資料庫中的資料進行更新(Cache概念)。


<br/>

<br/>

## Persistence Context
1. 每個持久物件都會與一個 Persistence Context 發生關聯，它可以管理持久物件的快取、dirty check(尚未commit到DB的待更改物件)、flush 等，牽涉著持久層的效能議題。

2. 所有在 Persistence 狀態的物件在 Persistence Context 中會有一個複製品，用於與持久物件進行比較，確定狀態是否有所改變，也就是執行所謂的dirty check。由於所有 Persistence 狀態的物件都會在Persistence Context 有一個複製品，而且他不會被自動移除，所以若在一個 Session 中載入過多的物件，將會導致記憶體用盡而得到 OutOfMemoryException。

<br/>

<br/>

## EntityManager
1. 為 JPA 中用來管理實體物件的類，由 EntityManger 和 PersistenceContext 一同管理，一個 PersistenceContext 可以被多個 EntityManger 同時管理，但 PersistenceContext 只有一個，唯一值。

    ```
    EntityManger <---Many---One---> PersistenceContext <---One---One---> Entity
    ```

2. 在 springboot 取得 EntityManager 實例，`@PersistenceContext` 是 JPA 的註解，而 @Autowired 是 Spring 的註解。由於 EntityManager 為線程不安全，@PersistenceContext 就是用來為每個線程建立一個 EntityManager，確保不會報錯。

    ```java
    public abstract class AbstractJpaDao {

        @PersistenceContext
        private EntityManager em;
    }
    ```

<br/>

<br/>

## flush() 與 commit() 差異
* flush() : 

    1. 主要作用就是要清理緩存(Hibernate的)，強制資料庫與 Hibernate 的緩存同步，已保證數據的一致性，以及確保緩存不會因為太大而OOM。
    2. 會向資料庫發送一系列的 sql 語句，並執行這些語句，但不會 commit，所以若只有使用 flush() 時，資料庫並不會更新。
    3. 預設在以下情況會呼叫 flush()

        * Transaction 進行 commit
        * 下一個查詢執行之前
        * 主動呼叫 Session 的 flush() 方法

* commit() :

    1. 在 commit() 中，會先執行 flush()。

<br/>

<br/>

## 快取
分為幾種快取 `Session level Cache` 、 `SessionFactory level Cache` 、 `Query Cache`。
```yml
# 是否開啟快取
# Session level Cache 沒辦法關，恆開啟
Spring: 
    jpa:
        show-sql: false
        hibernate:
            use-new-id-generator-mappings: false
        properties:
            hibernate:
                dialect: org.hibernate.dialect.PostgreSQLDialect
                cache:
                    use_query_cache: false  # 不使用
                    use_second_level_cache: false  # 不使用
```


* Session level : 
 
    1. 快取隨著 Session 建立而建立，隨著 Session 銷毀時銷毀。
    2. 會在使用 PK 載入資料或是 Lazy Initialization (例如: `session.load()`) 時作用。
    3. Session 會建立一個 Map 容器，並保留與目前 Session 發生關係的資料；當透過 PK 查找資料時(觸發快取)，會先在此 Map 中尋找並比對是否為同一個物件，若符合就取出，若不符會去撈資料庫，撈完會同時將資料儲存在此 Map。
    4. 若擔心快取資料太多，可以使用以下 Session 方法釋放資源。

        ```java
        Session session = sessionFactory.openSession();
        
        session.clear(); // 清除所有快取
        session.evict(entity);  // 清除指定物件
        ```
* SessionFactory level (二級快取， Second-level): 

    1. 由於上述 Session level 的快取必須是在 Session 開啟下起作用，猜測二級快取的使用情境應該是多線程之間的快取 (Session 是`線程不安全`， SessionFactory 是`線程安全`)。
    2. 二級快取可以跨越數個 Session，二級快取由同一個 SessionFactory 所建立的 Session 所共享，就算關閉其中一個 Session，另一個 Session 還是可以使用二級快取 (兩個 Session 需為同一個 SessionFactory 所建立)。
    3. Hibernate 本身未提供二級快取，而是使用第三方 EHCache 作為實現。

* Query :

    1. Hibernate 會保留執行過的查詢 SQL 與查詢結果，在下一次查詢時會看看 SQL 是否相同，並看看對應的資料庫表格是否有變動（Update/Delete/Insert），如果 SQL 相同且資料庫也沒有變動，則將 Query 快取中的查詢結果返回。
    2. 與二級緩存相依賴，大部分是用在頻繁查詢相同條件時才比較有用。
    3. `list()` 方法在讀取資料時，會利用到 Query 快取， 而 `iterate()` 則不會使用到 Query 快取功能，而是直接從資料庫中再查詢資料。


<br/>

<br/>

## References

> Hibernate3.3 Document
https://docs.jboss.org/hibernate/core/3.3/reference/en/html/index.html

> 狀態關係圖
https://openhome.cc/Gossip/HibernateGossip/EntityObjectLifeCycle.html

> Persistence Context
https://openhome.cc/Gossip/HibernateGossip/PersistenceContext.html

> Cache Level
https://openhome.cc/Gossip/HibernateGossip/SessionLevelCache.html