## Redis特點
1. 全名 `Remote Dictionary Server`，是一種記憶體的鍵值型NoSQL數據庫。
2. 儲存方式為鍵值型，value可以使用多種不同數據結構。
3. 命令執行時為`單線程(線程安全)`，每個命令具備原子性；補充，Redis 6.0 版本支持網路請求時可以多線程處理，但核心命令執行依然是單線程。
4. 低延遲、速度快 (基於記憶體、IO多路復用、良好的編碼)。
5. 支持數據持久化(可存至硬碟)。
6. 支持主從集群、分片集群(儲存的資料太大，分割到不同節點上的redis，水平擴展)。
7. 支持多語言客戶端(各種程式語言)。

<br/>

<br/>

## 基本命令
`選擇使用第幾個db`，不同db可以使用相同的key名稱
```sh
# 使用第1個
SELECT 1
```

<hr>

`help`，查詢命令格式
```sh
help [command]

# 例如，查詢 keys 的用法
help keys
```

<hr>

`KEYS`，使用key做查詢

注意: 由於 redis 是屬於單線程，資料量大的服務器中，使用 keys 查詢會對其他處理造成阻塞，所以`盡量不要在prod環境中使用keys命令`
```sh
# 查詢所有 keys 的值
keys * 

# 模糊查詢
keys a*  # 所有a開頭
keys *a  # 所有a結尾
keys a*a # 所有a開頭a結尾
```

<hr>


`EXISTS`，檢查該 key 是否存在
```sh
exists name1 # 返回 1 或 0，代表 true/false
```

<hr>

`EXPIRE`，設置到期時間，時間到期後該key會被自動刪除，此功能提供讓記憶體中的資料不會無限增長，可用在簡訊驗證碼需求上。

`TTL`，查看該key的剩餘到期時間。

```sh
# 需要先儲存一筆資料
set name apple

# 設置到期時間，單位為秒
EXPIRE name 20 

# 查看剩餘秒數
TTL name
```

<hr>

<br/>

<br/>

## 資料結構

> 官方網站 https://redis.io/docs/data-types/#strings

有5種基本類型:
* String
* Hash
* List
* Set
* SortedSet

其他類型 (由基礎類型實作)，除了這3種，還有其他類型
* GEO (Geospatial indexe，地理空間)

    ```
    {A: (120.3, 30.5) }
    ```

* BitMap (點陣圖)

    ```
    0110110101110101011
    ```

* HyperLogLog

    ```
    0110110101110101011
    ```


<br/>

<br/>