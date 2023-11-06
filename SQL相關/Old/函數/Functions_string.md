# CONCAT()
將columns的內容合併，可合併column內容或str
```sql
CONCAT(<col>, <str>, <col>);
```

# CONCAT_WS()
使用第一個參數的str將所有column串接起來
```sql
CONCAT_WS(<str>, <col>, <col>, <col>);
```
舉例
```sql
CONCAT_ws(' - ', id, title, name);   
# id - title - name
```

# SUBSTRING() , SUBSTR()
選擇顯示字串的範圍，使用index選取
```sql
SUBSTR(<str>, <start_index>, <end_index>);
# index 從1開始，非0開始
# 若只有<start_index>，會取 <start_index> 到最後
# 
```
舉例，取title的第1到5個字元
```sql
SUBSTR(title, 1, 5);         # index從1開始算，非0開始；第5個字串也會取
```
舉例，從3取到最後
```sql
title='123456789'
SUBSTR(title, 3);            # 輸出3456789
```
舉例，從-3取到最後
```sql
title='123456789'
SUBSTR(title, -3);           # 輸出789
```

# REPLACE()
將字串中的 old_str 取代成 new_str
```sql
REPLACE(<text>, <old_str>, <new_str>);
```

# REVERSE()
字串反轉
```sql
REVERSE(<str>);
```

# CHAR_LENGTH() , LENGTH()
計算字串長度
```sql
CHAR_LENGTH(<str>);
```
# UPPER()
轉換成大寫
```sql
UPPER(<str>);
```

# LOWER()
轉換成小寫
```sql
LOWER(<str>);
```

<br/>

<br/>

# LEFT() / RIGHT()
取字串`開頭/結尾`幾個字元
```sql
LEFT('ABCDEFG', 3)  -- 'ABC'

RIGHT('ABCDEFG', 3) -- 'EFG'
```

<br/>

<br/>

# REGEXP 
正則式，`^` 為開頭， `$` 為結尾， `.` 為萬用字元
```sql
-- 篩選 city 欄位以 a,e,i,o,u 開頭的資料
SELECT DISTINCT city FROM station WHERE city REGEXP '^[aeiou].*';
```