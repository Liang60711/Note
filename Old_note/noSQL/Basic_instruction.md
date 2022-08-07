# 連接/關閉 MongoDB 伺服器
以系統管理員身分執行
```shell
$ net start MongoDB     # 連接
$ net stop MongoDB      # 關閉
```

# 連接 database 
```shell
$ mongod                    # db在同一個資料夾
$ mongod --dbpath "<path>"  # 更改db的路徑
```

# db操作
顯示現在db
```
db
```

顯示所有db
```
show dbs
```
使用 db，若資料庫不存在，則新增 data 後會自動建立
```md
use <db_name>
```

