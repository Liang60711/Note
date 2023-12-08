# 預存程序（Stored Procedure）

### 概述
核心思想很簡單，就是一組經過`預先編譯的SQL語句`的封裝，由於可以封裝，可以隱藏一些複雜的商業邏輯或流程控制，`常用來執行SQL操作，而不是返回結果`。


執行過程: 儲存過程預先儲存在MySQL服務器上，需要執行的時候，客戶端只須向服務器端發出調用SP的命令，服務器端就可以把預先儲存的SQL語句全部執行。

<br/>

<br/>

### 優點
* 如果SQL查詢語句太過複雜或商業邏輯複雜導致查詢陳述句過長又龐大，可以用SP來撰寫SQL，因為用戶端只會傳SP Name給SQL Server，而不是一長串的SQL，`減少網路的傳輸量`。


### 缺點
* 除錯困難: 例如，SP中的SQL有上百行，執行時報錯，要除錯就會變得非常困難。


<br/>

<br/>

### 與視圖View、函數Function的比較
視圖View是虛擬表，是可以做DML沒錯，但通常不透過view對底層數據直接操作，還是以查詢居多。

而`預存程序Stored Procedure` 是程序化的SQL，可以直接操作底層數據，能夠實現一些更複雜的數據處理。

與函數Function的差別是，函數有返回值，`預存程序Stored Procedure`通常是沒有返回值的。

<br/>

<br/>

### 預存程序 建立

```sql
-- 1. DELIMITER 用處是先將預設的分隔符號由分號 ';' 換成 '//' 符號，以免寫 SQL指令 的時候中斷而報錯

-- 2. ()小括號裡面放參數，都沒填則是默認使用IN

DELIMITER //

CREATE PROCEDURE 預存程序名稱(IN|OUT|INOUT 參數名 參數類型, ...)
[characteristics ...]
BEGIN
    各種SQL指令
END //      -- 這邊要記得加分隔號

DELIMITER ; -- 再將分隔符號換回來
```

示範
```sql
DELIMITER //

CREATE PROCEDURE select_all_data()
BEGIN
        SELECT * FROM employees;
END //

DELIMITER ;
```
<br/>

<br/>

### 預存程序 調用
使用 `CALL + SP名稱` 調用
```sql
CALL select_all_data();
```

<br/>

<br/>
