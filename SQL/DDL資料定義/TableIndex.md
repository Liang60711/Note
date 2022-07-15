## Key 與 index 的差異
* Key 包含了兩個功能

    1. 約束 (constraint)
    2. 索引 (index), 也就是 key 功能上包含了 index.

## Primary Key
1. Unique constraint
2. Each table can only have one Primary key
3. `Cannot be null`
* 一定是非 null 且 unique，一個table只能有一個 Primary Key。

## Unique Key
1. Unique constraint
2. Each table can have multiple Unique keys
3. `Can be null`
* 可以是 null 且 unique，一個table可以有多個 Unique Key。


## Index (非唯一索引)
* 設定為非Unique的欄位值、可以重複，。
* 可以是 null ，一個table可以有多個 Index。
* 可使用複合索引，將多個欄位合併成索引。