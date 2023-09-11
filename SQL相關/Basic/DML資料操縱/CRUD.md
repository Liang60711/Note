# CREATE 
### 新增 data
```SQL
INSERT INTO <table_name> (<col1>, <col2>)
VALUES (<value1>, <value2>),
       (<value1>, <value2>);
```

# READ
查看整個 table
```SQL
SELECT * FROM <table_name>;
```
WHERE 篩選
```SQL
SELECT <col1>, <col2> FROM <table_name>
WHERE <condition>;     # 條件中，大小寫都可以
```
AS 改header名稱
```sql
SELECT <col1> AS <name1>, <col2> AS <name2> FROM <table_name>;
```

# UPDATE
修改 table
```SQL
UPDATE <table_name> SET <new1>, <new2>
WHERE <condition>, <condition2>;
```

# DELETE
刪除 table
```SQL
DELETE FROM <table_name>
WHERE <condition>, <condition2>;
```
刪除比較
```sql
DELETE FROM <table_name>;   # 刪除表中所有內容 (table還在)
DROP TABLE <table_name>;    # 刪除table (table已不在)
```

# 除錯
查看警告
```sql
SHOW WARNINGS;
```