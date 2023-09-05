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