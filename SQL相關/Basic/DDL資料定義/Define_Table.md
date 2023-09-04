# 資料定義
## customers
|customer_id|first_name|last_name|email|
|--|--|--|--|
|1|Boy|Apple|Apple@gmail.com|
|2|George|Orange|Orange@gmail.com|
|3|David|Melon|Melon@gmail.com|
|4|Blue|Mango|Mango@gmail.com|
</br>  

建立customers
```sql
$ CREATE TABLE customers
    (
        customer_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
        first_name  VARCHAR(100),
        last_name   VARCHAR(100),
        email       VARCHAR(100) DEFAULT 'None'
    );
```

## orders
|order_id|order_date|amount|customer_id|
|--|--|--|--|
|1|'2016/02/10'	|99.99|1|
|2|'2017/11/11'	|35.50|1|
|3|'2014/12/12'	|800.67|2|
|4|'2015/01/03'	|12.50|2|
</br>

```sql
CREATE TABLE orders(
    order_id      INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    order_date    DATE NOT NULL,
    amount        DECIMAL(8,2),
    customer_id   INT,
    FOREIGN KEY (customer_id) 
        REFERENCES customers(customer_id)
        ON DELETE CASCADE
);
```
</br>

## NOT NULL

* 當欄位為 NOT NULL 時，則在新增資料時該欄位一定要有值

## DEFAULT
* DEFAULT 用來設定欄位的預設值，當 INSERT 資料時若該欄位為 NULL 時則會採用預設值。

## PRIMARY KEY
* PRIMARY KEY 用來保證欄位在資料表中的唯一性
* 常與 AUTO INCREMENT 一起使用

## AUTO INCREMENT
* AUTO INCREMENT 欄位會自動增加資料筆數
* AUTO INCREMENT 欄位會是唯一的，自帶 UNIQUE 屬性

## FOREIGN KEY
* FOREIGN KEY 關係鍵是一個表中的一個 (多個) 屬性，用來與另一個資料表產生關聯。
* 常與 ON DELETE/UPDATE CASCADE 一起使用

新增 FOREIGN KEY
```sql
ALTER TABLE orders
ADD FOREIGN KEY (customers_id) REFERENCES customer(customers_id);
```
移除 FOREIGN KEY
```sql
ALTER TABLE orders
DROP FOREIGN KEY (customers_id);
```


## CASCADE
* ON DELETE/UPDATE CASCADE 若主表 (customers) 資料進行刪除/更新，則子表 (orders) 的資料也會一起連動進行刪改。
* 舉例，customers 表中的 customers_id = 1 刪除，則 orders 表中含有 customers_id = 1 (或關聯的 FOREIGN KEY = 1) 的資料也會一同刪除