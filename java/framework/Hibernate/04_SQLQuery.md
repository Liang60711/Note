## SQLQuery
1. 使用原生 SQL 語句。
2. 使用步驟: 

    1. 創建 SQLQuery 物件。
    2. 調用 SQLQuery 物件裡面的方法得到結果。


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

            // 1. 創建SQLQuery物件
            SQLQuery sqlQuery = session.createSQLQuery("SELECT * FROM user");

            // 2. 調用SQLQuery物件裡的方法，取得結果。
            // 這裡返回的不是 User 物件，是 List，與其他 API 不同
            List list = sqlQuery.list();    // [1, "name", "age"]

            // options: 讓返回的是 User 物件而不是 list
            sqlQuery.addEntity(User.class);
            List<User> userList = sqlQuery.list();


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