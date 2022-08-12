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
            "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">

    <hibernate-configuration>

        <session-factory>
            <!--  1. 配置資料庫信息(必須填)  -->
            <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
            <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernate</property>
            <property name="hibernate.connection.username">root</property>
            <property name="hibernate.connection.password">root</property>

            <!--  2. 配置hibernate信息(可選填)  -->
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
            <mapping resource="org/example/entity/User.hbm.xml"></mapping>

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