## Criteria 
1. 不需要寫 SQL 語句，直接使用物件的方法。

2. 使用步驟: 

    1. 創建 Criteria 物件。
    2. 調用 Criteria 物件裡面的方法得到結果。

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

            // 1. 創建Criteria物件
            Criteria criteria = session.createCriteria(User.class);

            // 2. 調用 query 物件裡的方法，取得結果。
            List<User> list = criteria.list();


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

<br/>

<br/>

## Criteria 