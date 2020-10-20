# NOW()
回傳現在DATETIME
```sql
SELECT NOW();
```

# CURDATE()
回傳現在DATE
```sql
SELECT CURDATE();
```

# CURTIME()
回傳現在CURTIME
```sql
SELECT CURTIME();
```

# 設定時區
```SQL
SET time_zome='+8:00';
```
# DATE_FORMAT()
將datetime格式化
```SQL
DATE_FORMAT(<datetime>, "%Y-%m-%d %H:%i:%S");
```
格式化
```sql
%Y      # 4位西元年
%y      # 2位西元年
%M      # 字母月份，January
%m      # 數字月份
%D      # 字母日，1st, 2nd, 3rd
%d      # 數字日

%W      # 字母星期幾，Subday, Saturday
%w      # 數字星期幾，0為週日，7為週六

%H      # 數字時(24H)
%h      # 數字時(12H)
%i      # 數字分
%S      # 數字秒
```

# DATEDIFF() 計算相差幾天
```SQL
SELECT DATEDIFF(<datetime>, <datetime>)
FROM <table_name>;
# 輸入 <date> 或 <datetime> 皆可
```

# DATE_ADD() 輸入間隔，計算間隔後時間
INTERVAL 為保留字元
```SQL
SELECT DATE_ADD(<datetime>, INTERVAL 30 DAY)
FROM <table_name>;
```

用 +/- INTERVAL ，不用DATE_ADD()，可一次間隔多個單位
```SQL
SELECT <datetime> 
    + INTERVAL 15 MONTH
    + INTERVAL 3 MINUTE
    - INTERVAL 1 SECOND
FROM <table_name>;
```

# TIMESTAMP 建立表格
1.建立content_ 時，會自動輸入 change_at，預設為NOW()  
2.修改資料時 (ON UPDATE) ，也會一併更新成 CURRENT_TIMESTAMP 時間
3.CURRENT_TIMESTAMP 與 NOW() 等價
```SQL
CREATE TABLE comment(
    content_    VARCHAR(100),
    change_at   TIMESTAMP DEFAULT NOW() ON UPDATE CURRENT_TIMESTAMP
);
```


# 從DATETIME取資料的函數
|函數|說明|
|--|--|--|
|DAY()|從DATETIME取幾號|
|DAYNAME()|從DATETIME取星期幾 (字母)|
|DAYOFWEEK()|從DATETIME取星期幾 (數字)|
|DAYOFYEAR()|找一年中第幾天|
</br>


