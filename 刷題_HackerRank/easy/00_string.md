## Weather Observation Station 7
欄位為 aeiou 結尾的資料
```sql
SELECT DISTINCT city FROM station WHERE city REGEXP '.*[aeiou]$';
```


<br/>

<br/>

## Weather Observation Station 8
欄位為 aeiou 開頭及結尾的字串
```sql
SELECT DISTINCT city FROM station WHERE city REGEXP '^[aeiou].*[aeiou]$';
```

<br/>

<br/>

## Weather Observation Station 9
欄位不含aeiou開頭的字串
```sql
SELECT DISTINCT city FROM station WHERE city REGEXP '^[^aeiou].*';
```

<br/>

<br/>

## Weather Observation Station 11
欄位不含 aeiou 開頭 `或` aeiou 結尾
```sql
SELECT DISTINCT city FROM station WHERE city NOT REGEXP '^[aeiou]' OR city NOT REGEXP '[aeiou]$';
```

<br/>

<br/>

## Weather Observation Station 12
欄位不含 aeiou 開頭 `且` aeiou 結尾，注意和上一題有區別
```sql
SELECT DISTINCT city FROM station WHERE city REGEXP '^[^aeiou].*[^aeiou]$';
```

<br/>

<br/>

## Higher Than 75 Marks
排序按照 name 欄位的最後3個字元
```sql
SELECT name FROM students WHERE marks > 75 ORDER BY RIGHT(name, 3), id;
```