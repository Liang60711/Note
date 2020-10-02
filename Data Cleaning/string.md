# 字串操作
## 長度
```python
len()
```
## 大小
z最大a最小，小寫比大寫大
```python
max()   
min()
```
## 檢查
```python
<str>.isalnum()             # 英文字母或數字
<str>.isalpha()             # 英文字母
<str>.isdigit()             # 數字
<str>.isidentifier()        # 合法的辨識字
<str>.islower()             # 小寫
<str>.isupper()             # 大寫
<str>.isspace()             # 空白字元
```
## 搜尋
```python
endswith(str1)              # 以 str1 結尾
startswith(str1)            # 以 str1 開頭
count()                     # 出現幾次
find(str1)                  # str1 最小索引值
rfind(str1)                 # str1 最大索引值
```
## 轉換
```python
capitalize()                # 回傳第一個字母大寫 
lower()                     # 回傳小寫
upper()                     # 回傳大寫
title()                     # 每個單字開頭大寫
swapcase()                  # 大小寫互換
replace(old, new)           # 舊換新字串
```
## 切割
```python
split()                     # 預設切割空白字元，若沒空白字元，轉換為list
splitlines()                # 切割換行符號\n，若沒\n，轉換為list
```
## 合併
```python
<str>.join(<list>)          # list以str連接
```

## 刪除前後空白
```python
strip()                     
```
## 刪除標點符號
正則式也可以
```python
import string

# string.punctuation = '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'
for i in <str>.split():
    print(i.strip(string.punctuation))
```


