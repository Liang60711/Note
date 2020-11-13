# Pandas資料處理
## 索引設定
重新產生數字索引的index，從0開始
```python
df.reset_index()                         
```
指定某columns為index
```python
df.set_index('<col>')                   
```
設定column為index後並且重新排序index
```python
df.set_index('<col>').reset_index()  
```
重新排序index 
```python
df.sort_index()
```
## 新增row
```python
df.append(pd.Series(['<col>','<col>','<col>'], index=df.columns), ignore_index=True)
```

## 分類
依照columns分類
```python
df.groupby('<col>')                    # 依照column分類 
df.groupby(['<col>','<col>'])          # 兩層分類 加 []
df.groupby('<col>').get_group()        # 從column中找分類
```
樞紐分析表
```python
df.pivot_table(index='',columns='',values='')
```
## 資料清洗
上下合併 (concatenate 縮寫)
```python
pd.concat([df1,df2],ignore_index=True)
```
左右合併，不需要[ ]
```python
pd.merge(df1,df2)
```
檢查空值
```python
df.info()
df.isna()
df.isnull()
df.isnull().any()
```
刪除空值 (需回存或inplace=True)
```python
df.dropna()                     # 只要row有一個nan就刪掉row
df.dropna(how='all')            # row 全部空白才刪除
df.dropna(subset=['<col>'])     # 刪除指定欄位的nan
```
刪除相同row 
```python
df.drop_duplicates()
```
填補空值 (需回存或inplace=True)
```python
df.fillna(value=0)              # 隨意數字
df.fillna(value=df.mean())      # 成平均值
df.fillna(method='ffill')       # 前一個值
df.fillna(method='bfill')       # 後一個值
```
填補空值 np.where()
```python
np.where(<condition>, <output1>, <output2>)     # 滿足condition輸出output1，否則輸出output2

np.where(df[0].isnull(), df[0].median, df[0])   # 舉例
```
```python
```
```python
```
```python
```
```python
```
