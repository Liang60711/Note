# DataFrame
## 建立名稱
```python
# 1
df=pd.DataFrame(data,index=<index>,columns=<columns>)

# 2
df.index=<index>
df.columns=<columns>
```
## 新增
新增columns
```python
df[<column>]=<value>
```
新增row
```python
df.loc[len(df)]=<value>
```

## 取值
取column值
```python
df[<col>]
df[[<col>,<col>,<col>]]
```
取row值
```python
# 顯性索引取值(包含終止值)
df.loc[<index>]
df.loc[<index>:<index>]

# 隱性索引取值(不包含終止值)
df[<index>:<index>]            # 預設iloc，兩個等價
df.iloc[<index>:<index>]
```
指定取值
```python
df.loc[:,:]
df.iloc[:,:]
```
條件取值
```python
df[<condition>]
```

## 排序
```python
df.sort_values(by='<col>', ascending=<bool>)
```
## 修改
修改columns名稱
```python
cols=list(df.columns)
cols[0]='<col_name>'
df.columns=cols
```
修改index名稱
```python
indexs=list(df.index)
indexs[0]='<index_name>'
df.index=indexs
```
修改columns值
```python
df[<col>]=values
```
修改columns值
```python
df.loc[<index>]=values
```
## 刪除
刪除row，axis=0，刪除columns，axis=1  
inplace=True可替代回存動作
```python
#刪除row
df=df.drop('<row>',axis=0)

#刪除columns
df=df.drop('<col>',axis=1)
df=df.drop(['<col>','<col>'],axis=1)
```

## 讀取檔案
```python
df=pd.read_csv('file.csv',encoding='utf-8-sig',index_col=0)     #index_col可將指定column設定成index
```
## 輸出檔案
```python
# 可以加上encoding='utf-8-sig'
df.to_csv('file.csv')
df.to_dict()
df.to_html('file.html')
df.to_json('file.json',force_ascii=False)
```
