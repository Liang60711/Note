## 1. 套件匯入
```python
from bs4 import BeautifulSoup
```

## 2. 建立BeautifulSoup物件
```python
import requests

r=requests.get(url)
soup=BeautifulSoup(r.text,'lxml')   # lxml解析速度較html5lib快
```

```python
soup.prettify()     # 格式化標籤成統一縮排
```

## 3. BeautifulSoup物件屬性
基本屬性
```python
soup.<tag>                  # tag是標籤名稱 可以是div, select, table
soup.<tag>.name             # 取標籤名稱 返回<tag>本身
soup.<tag>.string           # 取標籤字串 子標籤取不到
soup.<tag>.text             # 取標籤及子標籤所有合併字串
soup.<tag>.get_text('-')    # 取標籤及子標籤所有合併字串 並用 - 分隔

soup.<tag>.[<attribute>]    # 取tag屬性 也可以用get取
soup.<tag>.attrs            # 取得所有屬性值 回傳值格式為dict
```
向下定位
```python
soup.<tag>.<tag>            # 向下找，但只能找到第一個
soup.contents               # 回傳list，取所有子標籤
soup.children               # 回傳list_iterator，取所有子標籤，list_iterator型態需用for迴圈輸出

soup.descendants            # 回傳generator，取所有*子孫標籤，需用for迴圈輸出
```
向上定位
```python
soup.parent                 # 取父標籤
soup.find_parent()          # 函數，取父標籤

soup.parents                # 取所有父以上標籤
soup.find_parents()         # 函數，取所有父以上標籤
```
左右定位
```python
soup.text_sibling           # 取下一個兄弟標籤
soup.find_next_sibling()    # 函數，取下一個兄弟標籤

soup.previous_sibling         # 取上一個兄弟標籤
soup.find_previous_sibling()  # 取上一個兄弟標籤

```





