# DISTINCT
會將相同的資料篩掉，只取內容不同的資料
```SQL
SELECT DISTINCT <col> FROM <table_name>;
```
若為多columns，則會辨認columns完全一樣才篩掉
```SQL
SELECT DISTINCT <col1>, <col2>, <col3> FROM <table_name>;
```