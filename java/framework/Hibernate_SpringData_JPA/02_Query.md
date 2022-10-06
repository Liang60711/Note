## Query 
1. 不需要寫 SQL 語句，單需要寫 HQL (Hibernate query language) 語句。
2. SQL 和 HQL 區別: 

    * 使用 sql : 操作`表`和`欄位`
    * 使用 hql : 操作`實體類`和`屬性`

3. 使用步驟: 

    1. 創建 Query 物件。
    2. 調用 Query 物件裡面的方法得到結果。


<br/>

<br/>

## 範例，查詢整張表
```java
public class HibernateQuery {

    @Test
    public void testQuery() {
        SessionFactory sessionFactory = null;
        Session session = null;
        Transaction tx = null;
        try {
            sessionFactory = HibernateUtils.getSessionFactory();
            session = sessionFactory.openSession();
            // 開啟 transaction
            tx = session.beginTransaction();

            // 1. 創建物件，並寫hql語句
            Query query = session.createQuery("from User"); // 實體類名稱 User

            // 2. 調用 query 物件裡的方法，取得結果。
            List<User> list = query.list();

            // ... 

            tx.commit();
        }catch (Exception e) {
            e.printStackTrace();
            tx.rollback();  // rollback
        }finally {
            session.close();
            sessionFactory.close();
        }
    }

}
```