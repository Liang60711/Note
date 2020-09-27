## 建立陣列
```python
import numpy 

a=np.array([1,2,3,4])
```
array屬性
```python
a.ndim          # 檢視array屬性
```
```python
a.dtype         # 檢視資料型態
```
建立array
```python
np.arange(<start>, <end>, <step>)       # 建立連續array
```

```python
np.linspace(<start>, <end>, <分成幾分>)  # 等距array
```

```python
np.ones((<row>, <column>),dtype=int)    # 多維array，value都為1.浮點數，可以設定成整數
```
```python
np.zeros((<row>, <column>),dtype=int)  # 多維array，value都為0.浮點數，可以設定成整數
```

```python
np.eye(<n>)                             # 建立n x n array
```
```python
np.random.randn(<row>, <column>)        # 建立亂數
```
## 陣列取值
一維取值
```python
a=array([0,1,2,3,4,5,6,7,8,9])
a[2:8:2]                                # 輸出array([2,4,6])
a[::3]                                  # 輸出array([0,3,6,9])
```
二維取值
```python
a=np.arange(0,51,10).reshape(6,1)+np.arange(6)
a=array([
    [ 0,  1,  2,  3,  4,  5],
    [10, 11, 12, 13, 14, 15],
    [20, 21, 22, 23, 24, 25],
    [30, 31, 32, 33, 34, 35],
    [40, 41, 42, 43, 44, 45],
    [50, 51, 52, 53, 54, 55]])
```
```python
b=a[::2,::2]            # row,column逗號分隔
b=array([
    [ 0,  2,  4],
    [20, 22, 24],
    [40, 42, 44]])
```
布林取值
```python
a=array([10, 11, 12, 13, 14, 15, 16, 17, 18, 19])
a[a%2==0]=array([10, 12, 14, 16, 18])        # 使用[條件]取值
```


## 陣列運算(一維)
總和
```python
.sum()
```

平方值
```python
np.sqrt()
```
最小值索引
```python
.argmin()
```
最大值索引
```python
.argmax()
```

## 陣列運算(二維)
總和
```python
.sum(axis=0)        # columns總和
.sum(axis=1)        # row總和
.sum(axis=0).sum()  # 總和
```
最小值索引
```python
.argmin(axis=0)     # 各columns的最小值索引
```

陣列相乘
```python
a=array([[5, 1, 2, 1],
         [1, 2, 2, 5],
         [4, 2, 2, 4]])
b=array([[1, 1, 1, 1],
         [1, 1, 1, 1],
         [1, 1, 1, 1]])
```
```python
a*b=array([[5, 1, 2, 1],
         [1, 2, 2, 5],
         [4, 2, 2, 4]])
```
內積
```python
a=array([[5, 1, 2, 1],
         [1, 2, 2, 5],
         [4, 2, 2, 4]])
b=array([[1, 1, 1],
         [1, 1, 1],
         [1, 1, 1],
         [1, 1, 1]])
```
(3,4)矩陣和(4,3)矩陣，內積後成為(3,3)矩陣
```python
c=np.dot(a,b)
c=array([[ 9,  9,  9],
         [10, 10, 10],
         [12, 12, 12]])
```
## 陣列函數
維度轉換
```python
.reshape()
```
多維轉換成一維
```python
.ravel()
```
增加一個維度
```python
a=array([1,2,3,4])
b=a[:, np.newaxis]

b=array([[1],
         [2],
         [3],
         [4]])
```
## 陣列排序
排序
```python
.sort(axis=0)           # columns排序
.sort(axis=1)           # row排序

np.sort(<array>,axis=0) # 會回傳值
```
索引排序
```python
idx=<array>.argsort()   # 數值由小到大排序，並取index
<array>[idx]            # 中括號取值
```
## 開啟csv檔案
```python
npf=np.genfromtxt('csvfile.csv',delimiter=',',skip_header=1)
```