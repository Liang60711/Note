## 1. 套件匯入
```python
import requests
```
## 2. requests.get()
### 送出 get 請求
```python
params={key:values}
r=requests.get(url,params=params)   # (網址, 參數)
r=requests.get(url,cookies=cookies) # (網址, cookies)
r=requests.get(url,headers=headers) # (網址, headers)
r=requests.get(url,auth=<tuple>)    # (網址, 認證資訊)
r=requests.get(url,timeout=<int>)   # (網址, 請求時間(sec)) 時間太短會報錯

```
## 3. requests.post(url, data=data) 以html表單回傳
```python
FormData={key:values}
r=requests.post(url,data=FormData)  # (網址, 參數)
```


## 4. 回傳值屬性
```python
r.status_code   	# 2xx = Success（成功）
                	# 3xx = Redirect（重新導向）
                	# 4xx = User error（客戶端錯誤）
                	# 5xx = Server error（伺服器端錯誤）
r.status_code == requests.codes.ok  	  # 回傳布林值 成功/失敗
r.status_code == requests.codes.all_good  # 等價requests.codes.ok
r.raise_for_status()    		  # 函數取得進一步資訊
```

```python
r.content   # 位元組資料 二進位內容
```
```python
r.json      # 若網站為json取得json格式 可用r.json()函數取資料
```

```python
r=requests.get(url,stream=True) # stream=True 下載大檔案 用stream模式 以免記憶體不足
r.raw           # Raw Socket Response
r.raw.read(10)  # 顯示前10個string，或不帶入數字
```

```python
r.headers             # 取標頭資訊
r.headers[<Key>]      # 標頭為dict 取value值
r.headers.get(<Key>)  # 等價 r.headers[Key]
```

```python
r.cookies           # 取cookies
```

```python
r.encoding=='utf-8' # 更改編碼
```
## 5.例外處理
requests.exceptions:
```python
RequestException    # HTTP請求錯誤
HTTPError           # 網址或式參數錯誤
ConnectionError     # 網路連線 DNS異常
Timeout             # 超過請求時間
TooManyRedirects    # 重新轉址超過最大值
```

## 6. 檔案操作
寫入檔案
```python
import requests

url='https://www.google.com.tw/'
r=requests.get(url)
with open('google.txt','w',encoding='utf-8') as f:
    f.write(r.text)
    print('已寫入檔案')
```
讀取檔案
```python
with open('google.txt','r',encoding='utf-8')as f:
    # 區塊
    print(f.read())     
    # list
    for l in f.readlines():
        print(l)
```

## 7. 函式化
```python
def getSoup(url):
    try:
        r=requests.get(url)
    except requests.exceptions as e:
        print(e)
    else:
        r.encoding='utf-8'
        soup=BeautifulSoup(r.text,'lxml')
        return soup
    return None
```

