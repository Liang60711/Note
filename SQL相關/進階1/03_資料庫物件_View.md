# 視圖 / 檢視 / View

### 概述

1. 解決什麼問題 : 
    
    * 視圖可以幫助我們使用表的一部分(只顯示某欄位或某數據)，而不是所有的表(效能)。
    
    * 可以針對不同的用戶制定不同的查詢視圖(權限管理)。

2. 視圖是一種`虛擬表`，`本身沒有真正的數據`，真正的數據還是存在資料表中，占用的內存很少。

3. 視圖建立在已建立資料表的基礎上，`所以當資料表數據發生改變時，視圖也會更改`。

4. 反之，`對視圖進行 CREATE、UPDATE、DELETE 操作時，資料表的數據也會更改`，因為視圖數據的refernce也都是資料表裡面的數據。

5. 視圖本身的刪除，不會對資料表有影響。

6. 可以將視圖理解為 `存儲起來的SELECT語句`，小型的資料表可以不使用視圖，但大型資料表中，表較複雜，視圖的價值就出現了，可以將經常查詢的結果放到視圖虛擬表中，提升使用效率。


<br/>

<br/>

### 視圖優點
1. 操作簡單 : 不用每次都寫複雜的SQL語句，寫一次，存在視圖裡就行。

2. 減少數據冗餘

3. 數據安全 : 視圖可控制權限，讓用戶只能查看特定欄位。


### 視圖缺點

1. 維護成本高，底層的資料表若執行DDL修改資料型別，所有連動的試圖都要一起維護。
 
<br/>

<br/>

### 視圖創建 CREATE

在 `CREATE VIEW` 語句中嵌入子查詢
```sql
CREATE VIEW 視圖名稱 [(欄位別名)]
AS 查詢語句
-- [WITH [CASCADE|LOCAL] CHECK OPTION]
```

```sql
-- 創建
CREATE VIEW view1
AS 
SELECT * FROM `new_order`;
```

```sql
-- view 的欄位名稱可以使用 alias
-- 方法1: 寫在子查詢
CREATE VIEW view2
AS (
    SELECT user_id u_id,
           total_amount amount
    FROM `new_order`
);

-- 方法2: 拉出來寫，兩者等價
CREATE VIEW view2 (u_id, amount)
AS (
    SELECT user_id,
           total_amount 
    FROM `new_order`
);
```

```sql
-- 子查詢使用 WHERE 來篩選可以看到的數據
CREATE VIEW view3
AS (
    SELECT * FROM `new_order`
    WHERE total_amount  > 10000
);
```

```sql
-- group by
CREATE VIEW view4
AS (
    SELECT user_id, AVG(total_amount)
    FROM `new_order`
    GROUP BY user_id
);
```

<br/>

<br/>

### 視圖查看 SELECT
有4種方法

1. `SHOW TABLES` : 查看所有 TABLE 及 VIEW

2. `DESC view_name`: 查看單一視圖

3. `SHOW TABLE STATUS LIKE 'view_name'` : 查看視圖屬性資訊，如果在 cmd 中，可以加上 \G，倒置查看，`SHOW TABLE STATUS LIKE 'view_name'\G`。

4. `SHOW CREATE VIEW view_name` : 查看詳細資訊

<br/>

<br/>

### 視圖修改 UPDATE

準確來說，是修改是圖中的數據，同樣是使用 UPDATE 語法，當視圖修改時，資料表也會一併修改，反之亦然。
```sql
UPDATE view_name
SET salary = 10000
WHERE employee_id = 101;
```

<br/>

<br/>

