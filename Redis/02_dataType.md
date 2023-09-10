## String
依據String的類型，又可以分為3種

* String
* int
* float

不管是哪種格式，底層都是以 byte 儲存。

<br/>

<br/>

## String的指令: 

`set / get / del`，更新 / 查詢 / 刪除
```sh
# 新增或修改
set name rico

# 查詢
get name

# 刪除(可刪多筆)
del name1 name2 name3

> (integer) 1 # 返回刪除的數量
```

<hr>

`MSET` : 批量添加多個String類型的資料

`MGET` : 輸入多個key，一次可以取多個value


```sh
# 格式為 mset k1 v1 [k2 v2...]
mset k1 v1 k2 v2
```
```sh
# 取多個值
mget k1 k2
```

<hr>

`INCR`，increase，可以將String類型中，int的類型自增1 (只有 int 類型的適用，float 類型會報錯)

```sh
set age 18

incr age # 返回 (Integer) 19
```

`INCRBY`，同上，但可以指定增加多少數字
```sh
set age 18
incrby age 2 # 返回 (Integer) 20

# 如果要自減，可加負號
incrby age -2
```

`DECR` : 同INCR規則，自減1

`DECRBY` : 同INCRBY，自減n

<hr>

`INCRBYFLOAT`，只適用於 float，其他類型使用會報錯

```sh
set f1 1.14

# 必須指定增長多少
incrbyfloat f1 3.14
```

<hr>

`SETNX`，添加一筆String類型的資料，若key已經存在，則不執行 (檢查後新增)

```sh
set age 18

setnx age vvv # 返回 (integer) 0，key已存在時，不覆蓋
```

有2種新增方式，以下2種等價
```sh
#1 使用 setnx
setnx age 18

#2 nx寫在後面
set age 18 nx
```

<hr>

`SETEX`，新增一筆資料，並設置expire時間

```sh
# 格式 SETEX key seconds value
SETEX token 60 cd1a5
```

一樣可以把 ex 拆開來寫，等價
```sh
SET token cd1a5 ex 60
```

<br/>

<br/>

## Hash
Hash 資料結構，為無序的字典，value 中分為 `field` 和 `value`

<img width='80%' src='../_image/Snipaste_2023-09-10_17-33-43.png'>

<br/>

`HSET` : 添加或修改 hash 類型的 key, field, value

`HGET` : 獲取 hash 類型的 value (需輸入 key 和 field)

`HMSET` : 批量添加 hash 類型資料

`HMGET` : 批量獲取 hash 類型資料

```sh
# HSET key field value
HSET heima:user:1 name Jack
HSET heima:user:1 age 21

# HGET key field
HGET heima:user:1 name

# HMSET key field value [field value...]
HMSET heima:user:2 name Rose age 18 # 一次存進多個 field

# HMGET key field [field...]
HMGET heima:user:1 name age
```

<hr>

`HGETALL` : 獲取 hash 類型中，key 的所有 field 和 value

`HKEYS` : 獲取一個 hash 類型中，key 的所有 field

`HVALS` : 獲取一個 hash 類型中，key 的所有 value

```sh
# HGETALL key
HGETALL heima:user:1 # 返回 k1 v1 k2 v2，鍵值交互返回

# HKEYS key
HKEYS heima:user:1 # 返回 k1 k2 ...

# HVALS key
HVALS heima:user:1 # 返回 v1 v2 ...
```

<hr>

`HINCRBY` : 讓 hash 類型的 field 自增指定數字 (value 必須為 Integer 才能自增)


```sh
# HINCRBY key field
HINCRBY heima:user:2 age 3 # age值增加3
```

<hr>

`HSETNX` : 添加一個 hash 類型，指定 key 的 field 值，`field 必須尚未建立，否則不執行`。

```sh
# HSETNX key field
HSETNX heima:user:1 age 18 # 已經存在此 field，所以不執行
HSETNX heima:user:1 sex female # 會執行
```

<br/>

<br/>

## List
與 java 中的 `LinkedList` 類似，可以視為一個雙向鏈表結構，可支持`正向查詢`和`反向查詢`。

Redis List 特點還有以下幾點:

1. 有插入順序
2. 元素可以重複
3. 插入和刪除快速
4. 查詢速度一般(需遍歷)

<br/>

<br/>

`LPUSH` : 向 List 左側(頭部)插入一個或多個元素

`LPOP` : 移除並返回列表左側的`第一個`元素，沒有則返回 nil。

`RPUSH` : 向 List 右側(尾部)插入一個或多個元素

`RPOP` : 移除並返回列表右側的`第一個`元素

補充 : `LPOP` 和 `RPOP` 在高版本可以一次取多個元素。

```sh
# LPUSH key element [element...]
LPUSH users 1 2 3 # 因為是從左邊添加元素，所以 List 順序會是 3 -> 2 -> 1 

# LPOP key
LPOP users # 呈上，返回元素 3，而 List 剩下 2 -> 1

# RPUSH key element [element...]
RPUSH users 1 2 3 # List 順序為 1 -> 2 -> 3

# RPOP key
RPOP users 
```

`LRANGE` : 返回索引(index)範圍內的所有元素，常用*

```sh
# LRANGE key start end
LRANGE users 0 -1 # 取全部 list 元素
```

<hr>

`BLPOP` : (b，block 阻塞) 和 LPOP 類似，只不過在沒有元素時可以指定等待時間，若時間到還沒有元素則返回 nil。

`BRPOP` : 同上。

```sh
# BLPOP key timeout
BLPOP users 10 # 單位為秒

# BRPOP key timeout
BRPOP users 10
```