# Character Types 文字類型
## CHAR
* 儲存固定長度
* 讀取速度較快
* 較占儲存空間
* 多用在性別(M/F), 是非題(Y/N)

## VARCHAR
* 儲存非固定長度
* 讀取速度較慢
* 較省儲存空間  
</br>

# Numeric Types 數字類型
## INT
* 儲存空間 4 bytes
* 精確整數數值

## DECIMAL
* 範圍 -2<sup>-31</sup>	 ~ 2<sup>31</sup>-1
* 儲存空間 5~17 bytes
* DECIMAL(5,2) ，5代表總共可儲存的位數，2代表小數以下可儲存的位數  
* 若要儲存 < 1,000,000.00 的數字資料，資料最大值為 999.999.99，所以應設 DECIMAL(8,2)
</br>

## FLOAT
* 浮點數，4 Bytes (有效位數 7 位)

## DOUBLE
* 倍精度浮點數，8 Bytes (有效位數 15 位)

|類型|儲存位元|精度|
|--|--|:--:|
|FLOAT|4 Bytes|~7 digits|
|DOUBLE|8 Bytes|~15 digits|
|DECIMAL|16 Bytes|無精度問題|  
</br>

# Date and Time Types 日期和時間類型
## DATE
* 格式為"YYYY-MM-DD"
## TIME
* 格式為"HH-MM-SS"
## DATETIME
* 格式為"YYYY-MM-DD HH-MM-SS"

## TIMESTAMP
* 範圍從 "1970-01-01 00:00:01" 至 "2038-01-19 03:14:07"
* 提供自動更新功能 CURRENT_TIMESTAMP

|類型|儲存位元|範圍|
|--|--|:--:|
|DATETIME|8 Bytes|1000~9999年|
|TIMESTAMP|4 Bytes|1970~2038年|