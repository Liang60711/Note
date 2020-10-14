# Table 
### 建立 table
* NOT NULL 不允許空值
* DEFAULT  若為空值，則填入默認值
* PRIMARY KEY 不重複之index，AUTO_INCREMENT自動產生
```sql
$ CREATE TABLE <table_name>
    (
        id     INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
        name   VARCHAR(100) NOT NULL,
        age    INT,
        number INT DEFAULT 'None'
    );
```
### 查看 table
```sql
$ SHOW TABLES;
```
### 查看 table 的 columns ，兩種皆可
```sql
$ SHOW COLUMNS FROM <table_name>;
```
```sql
$ DESC <table_name>;
```
### 刪除 table
```sql
$ DROP TABLE <table_name>;
```