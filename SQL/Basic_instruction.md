# Database 

### 查看所有databases
```sql
$ SHOW DATABASES;
```
### 創建database
```sql
$ CREATE DATABASE <database_name>;
```
### 刪除database
```sql
$ DROP DATABASE <database_name>;
```
### 切換database
```sql
$ USE <database_name>;
```
### 查看目前使用的database
```sql
$ SELECT database();        # 若刪除或尚未use 顯示null
```

# Table
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
