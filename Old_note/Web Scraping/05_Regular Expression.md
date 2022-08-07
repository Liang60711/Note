## 正規表達式
單一搜尋 re.search()
```python
import re 

result=re.search(<str>, <text>)     # str為正規表達式，text為要搜尋的字串

result.strat()                      # 字元起始index
result.end()                        # 字元結束index
result.span()                       # tuple(起始index, 字元結束index)
result.group()                      # 輸出字元
```
複數搜尋 re.findall()
```python
re.findall(<str>, <text>)           # 回傳list
```

字元集
```python 
[abc]           # 包含abc
[abc{]          # 包含abc，符號

[A-Z]           # 包含英文大寫
[a-z]           # 包含英文小寫
[a-zA-Z]        # 包含英文
[0-9]           # 包含數字0-9

[^abc]          # 不包含abc，[^] 表示之外
```


```python
\w              # 包含英文字、數字、底線，即 [A-Za-z0-9_]
\W              # \w以外，即 [^A-Za-z0-9_]

\d              # 即 [0-9]
\D              # 即 [^0-9]

\s              # 空白字元，即 [\n\t\r\f]
\S              # 即 [^\n\t\r\f]
```

比較字元
```python
.               # 萬用字元，代表任何字元，除了換行
?               # 0次或1次
*               # 0次以上
+               # 1次以上
|               # 或，等價 or，需用小括號(a|b)

^               # 比對字串開頭，例如 ^The 搜尋開頭為The..的字串
$               # 比對字串結尾

{n}             # 出現n次
{n,m}           # 出現n~m次
{n,}            # 至少出現n次
```




搜尋e-mail
```python
'\w+@\w+\.\w+[\.\w]*'   # .為萬用字元，需改成\.
                        # com結尾或是com.tw結尾
```

圖片網址
```python
'.+\.[jpg,png,gif]+'
```
網址
```python
[a-zA-z]+://[^\s]*
```

中文字
```python
[\u4e00-\u9fa5]         # 中文編碼
```

台灣手機
```python
phone=phone.replace('-','')      # 先把所有-取除
re.findall('[+886,0]+\d+',phone)
```