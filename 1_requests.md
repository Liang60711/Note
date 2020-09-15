## 1. 套件匯入
---
```python
import requests
```
## 2. requests.get(url, params=params)
---
### 送出 get 請求
```python
params={key:values}
r=requests.get(url,params)  # (網址, 參數)
```

### 狀態碼
```python
r.status_code   # 200成功，404失敗
```


### 函式化
```python
def getSoup(url):
    r=requests.get(url)
    if r.status_code==200:
        soup=BeautifulSoup(r.text,'lxml')
        return soup
    return None
```
## 3. requests.post(url, data=data) 以html表單回傳
```python
FormData={key:values}
r=requests.post(url,data=FormData)  # (網址, 參數)
```
