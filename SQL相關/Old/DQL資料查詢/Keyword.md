# DISTINCT
會將相同的資料篩掉，只取內容不同的資料
```SQL
SELECT DISTINCT <col> FROM <table_name>;
```
若為多columns，則會辨認columns完全一樣才篩掉
```SQL
SELECT DISTINCT <col1>, <col2>, <col3> FROM <table_name>;
```

# ORDER BY
排序預設ASC，排序由小到大
```sql
SELECT <col> FROM <table_name> ORDER BY <col>;
# 等價 SELECT <col> FROM <table_name> ORDER BY <col> ASC; 
```
可改成DESC，排序由大至小
```sql
SELECT <col> FROM <table_name> ORDER BY <col> DESC;
```
ORDER BY 數字
```sql
SELECT <col1>, <col2>, <col3> 
FROM <table_name> ORDER BY 1;   # 使用<col1>作為排序標準
```
舉例，從books表格中，依照author排序(由小至大)，再以title排序(由大至小)
```sql
SELECT book_id, title, author
FROM books ORDER BY 3 ASC, 2 DESC ;
```

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


# LIMIT
取前幾5筆資料
```SQL
SELECT * FROM <table_name> LIMIT 5;
```
從第n筆開始，取m筆資料
```sql
SELECT * FROM <table_name> LIMIT n,m ;
```
舉例，從books表格中，依照book_id排序(由大至小)，取第11筆開始的10筆資料
```sql
SELECT * FROM books ORDER BY book_id DESC LIMIT 11,10;
```

# LIKE , NOT LIKE
特定模式 (Pattern) 為條件來搜尋資料表中的特定資料  
WHERE+LIKE 一起使用
```SQL
SELECT * FROM <table_name> WHERE <col> LIKE '%<str>%' ;
```
LIKE + 搜尋方式
```sql
LIKE '%'            # 任何資料
LIKE 'ABC%'         # ABC 開頭的資料
LIKE '%ABC'         # ABC 結尾的資料
LIKE '%ABC%'        # 包含 ABC 的資料
LIKE '__'           # 任何兩個字節的資料 (一個 _ 代表一個字母)
```
保留字元搜尋
```sql
LIKE '%\%%'         # 含有 % 字元的資料
LIKE '%\_%'         # 含有 _ 字元的資料
```

# AND , && , OR , ||
通常搭配 WHERE 一同使用
```sql
SELECT * FROM <table_name>
WHERE <condition> AND <condition>;
```
```sql
SELECT * FROM <table_name>
WHERE <condition> OR <condition>;
```

# BETWEEN
尋找範圍，資料型態 數字型態 或 DATETIME 皆可
```SQL
SELECT * FROM <table_name>
WHERE <col> BETWEEN <range1> AND <range2>;
```
若要測試DATETIME，可將str 轉成 datetime
```SQL
CAST("<datetime>" AS DATETIME);
```

# IN , NOT IN
搜尋是否存在於column中
```SQL
SELECT * FROM <table_name>
WHERE <col> IN (<str1>, <str2>, <str3>);
```
檢查多個選項，比 AND、OR 簡潔，舉例
```sql
SELECT * FROM books
WHERE SUBSTR(title, 1, 1) IN ('C', 'S')     # title為C或S開頭的資料
```

# CASE
條件式 WHEN, THEN, ELSE, END
```sql
SELECT 
    CASE
        WHEN <condition1> THEN <result1>
        WHEN <condition2> THEN <result2>
        ELSE <result3>
    END AS <col_name>
FROM <table_name>;
```
舉例
```sql
SELECT title, author,
    CASE
        WHEN COUNT(*) = 1 THEN '1 book'
        ELSE CONCAT(COUNT(*), ' books')
    END AS 'count'
FROM book
GROUP BY author;
```

# HAVING
HAVING(函數條件)；WHERE 和 HAVING 的區別在於與 GROUP BY 子句一起使用時，WHERE 用於在分組之前過濾 column，HAVING 用於在分組後過濾數據，與聚合函數一起使用。

WHERE 和聚合函數一起使用會報錯
```sql
SELECT item, cost
FROM shop
GROUP BY item
HAVING SUM(cost) > 1500;
```

原文網址：https://kknews.cc/code/vrgvx8q.html

# EXISTS
檢查**內查詢**是否有結果，如果有，就會執行外查詢；如果沒有，整個查詢就不會產生結果。
```SQL
SELECT <col>
FORM <table1>
WHERE EXISTS
    (SELECT * FROM <table2> WHERE <condition>)
```
舉例
```sql
SELECT name, SUM(cost)
FROM person
WHERE EXISTS    -- 若內查詢有結果才會繼續外查詢
    (SELECT * FROM country  
    WHERE city="Taipei");
```


# 取別名
都是在 `col` 後面空一個加上別名，table 和 col 都可以。
```sql
SELECT S.store_name Store, SUM(S.total_sales) "Total Sales"     -- 3. 再取 column 別名
FROM store_information S    -- 1.先取 table 別名
GROUP BY S.store_name       -- 2.用 table 別名分組
```

<br/>

|新別名|Store|Total Sales|
|--|--|--|
|舊名稱|store_name|total_sales|

<br/>

若要使用 `AS` 取別名，與空格效果相同
```sql
SELECT S.store_name AS Store, SUM(S.total_sales) AS "Total Sales"
FROM store_information AS S
GROUP BY S.store_name;
```