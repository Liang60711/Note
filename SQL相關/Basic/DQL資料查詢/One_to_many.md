# CROSS JOIN 
笛卡爾乘積，會得到 (table1 總數 * table2 總數) 筆資料

```sql
SELECT <col1>, <col2>, <col3>
FROM        <table_name1>
CROSS JOIN  <table_name2>;
# <col1>, <col2>, <col3> 可來自不同的table
```

或是
```sql
SELECT  <col1>, <col2>, <col3>
FROM    <table_name1>, <table_name2>
```

# INNER JOIN
為 table_name1 和 table_name2 的交集，即兩者KEY有重複才會符合
```sql
SELECT <col1>, <col2>
FROM        <table_name1>
INNER JOIN  <table_name2>
    ON <table1.id> = <table2.id>;
```

# LEFT JOIN
取左側資料表 table_name1 的全部資料 和 符合條件的 table_name2 資料，若沒有匹配的資料值就會顯示為 NULL
```sql
SELECT <col1>, <col2>
FROM       <table_name1>
LEFT JOIN  <table_name2>
    ON <table1.id> = <table2.id>;
```

# RIGHT JOIN
取右側資料表 table_name2 的全部資料 和 符合條件的 table_name1 資料，若沒有匹配的資料值就會顯示為 NULL
```sql
SELECT <col1>, <col2>
FROM        <table_name1>
RIGHT JOIN  <table_name2>
    ON <table1.id> = <table2.id>;
```
# IFNULL() 函數
LEFT JOIN, RIGHT JOIN 不匹配的值會產生NULL，使用IFNULL() 修改值
```sql
IFNULL(<col> , <value>)
```
舉例，若 SUM(aount) 為空值就填入0
```sql
SELECT 
    first_name,
    title, 
    IFNULL(SUM(aount), 0)
FROM customers
LEFT JOIN  orders
    USING customers.id;     
# ON 兩個欄位名稱相同，可以用 USING 替代
```

# LEFT JOIN 和 RIGHT JOIN 問題
兩個寫法，值會一樣，但 <table_name1> 和 <table_name2> 位置會相反
```sql
SELECT * FROM <table_name1>
LEFT JOIN <table_name2>
    ON table_name1.id = table_name2.id;
```

```sql
SELECT * FROM <table_name2>
RIGHT JOIN <table_name1>
    ON table_name1.id = table_name2.id;
```
