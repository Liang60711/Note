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