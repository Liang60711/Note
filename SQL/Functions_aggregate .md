# GROUP BY
依照 columns 進行分類，可以使用聚合函數
```SQL
SELECT <col1>, <col2>, aggregate_func(<col3>)
FROM <table_name> GROUP BY <col1>;
```
若 GROUP BY 多個 column 時，則會先合併 column，確認是否為同一筆資料
```sql
SELECT <col1>, <col2>, aggregate_func(<col3>)
FROM <table_name> GROUP BY <col1>, <col2>;
```

# COUNT()
計算總資料數
```SQL
SELECT COUNT(*) FROM <table_name>;
```
計算column中有幾筆不同的資料  
COUNT() + DISTINCT
```sql
SELECT COUNT(DISTINCT <col>)
FROM <table_name>;
```
舉例，DISTINCT會先合併三個columns，再計算資料數，因此若有author相同的資料，則不會少算
```sql
SELECT COUNT(DISTINCT book_id, title, author)
FROM books;
```

# MIN() , MAX()
聚合函數
```SQL
SELECT <col>, aggregate_func(<col>)
FROM (table_name);
```
---
|book_id|title|author| stock_number |
| :--: | :--: | :-- | --: |
|1|apple|Ben|500|
|2|badboy|Candy|300|
|3|catgood|Dove|1000|
<br>  

舉例，title 和 MAX(stock_number) 欄位各自獨立，所以會錯誤
```sql
SELECT title, MAX(stock_number)
FROM books;

# 輸出會是 apple 和 1000 (title 會取第一筆)
```
應該要寫成 (不用聚合函數)
```sql
SELECT title, stock_number
FROM books ORDER BY stock_number DESC LIMIT 1;
# 輸出 catgood 和 1000
```
# SUM()
將column數值加總
```SQL
SELECT <col1>, SUM(<col2>)
FROM <table_name>;
```

# AVG()
將column數值平均
```SQL
SELECT <col1>, AVG(<col2>)
FROM <table_name>;
```