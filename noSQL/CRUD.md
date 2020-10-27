# CREATE 建立
## insertOne 和 insertMany 會回傳ObjectId，insert 不會回傳
## insertOne 
```md
db.collection.insertOne(<document>)
```

## insertMany 
```md
db.collection.insertMany(<documents>)
```
## insert 可多可單
```md
db.collection.insert(<documents>)
```

# READ 查詢
## find 找所有資料 (顯示前20筆)
```md
db.collection.find()
```
```md
db.collection.find().toarray()
# 顯示所有的資料
```

## 指定查詢條件
```md
db.collection.find(<query>, <projection>)  
# query         查詢條件
# projection    選擇要顯示的key
```
舉例1
```java
{
    "_id" : <value>,
    "name" : { "first" : <string>, "last" : <string> },       // embedded document
    "birth" : <ISODate>,
    "death" : <ISODate>,
    "contribs" : [ <string>, ... ],                           // Array of Strings
    "awards" : [
        { "award" : <string>, year: <number>, by: <string> }  // Array of embedded documents
        ...
    ]
}
```
篩選embedded document，巢狀結構，向內搜尋
```md
db.collection.find({"name.first":"Hopper"})
# key一定要雙引號 "" 
```


篩選Array of Strings
```md
db.collection.find({contribs:"red"})
# 若list內有符合的string就OK，不用寫出所有string
```






舉例2，使用projection篩選顯示的key
```md
db.collection.find({}, {name:1, _id:0})
# 1為true，0為false
# _id預設為1
```
## findOne 找符合的第一筆資料
```md
db.collection.findOne(<documents>)
```


# DELETE 刪除

```md
db.collection.deleteOne()
```

```md
db.collection.deleteMany()
```

# UPDATE 修改

## updateOne 修改首個
```md
db.collection.updateOne(<query>, <update>, <upsert>)
# query     篩選條件
# update    要修改的數據項, 配合修改操作符
# upsert    bool值，預設 false；若為 true，未搜尋到符合結果則插入新 document
```

## updateMany 修改所有
```md
db.collection.updateMany(<query>, <update>, <upsert>)
```

## update 修改首個 或 修改所有
```md
db.collection.update(<query>, <update>, <upsert>, <multi>)
# multi     bool值，預設 false，同 updateOne；若為 true，同 updateMany
```
