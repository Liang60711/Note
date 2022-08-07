# Decorators
* 目的: 降低 Code 重複性，靈活度高
* python 和 javascript 相同，function 都是可以當作物件來傳遞
* 會有需要裝飾的 func() 和裝飾 decorator()

```python
# decorator function
def my_decorator(func):

    def wrap():
        print('Code before executing func')
        func()
        print('Code After executing func')
    return wrap     # 這邊也是 return 參數，需要有物件接收

# 需要裝飾的 func()
def func():         
    print('This is a function!')

# 執行
func = my_decorator(func)   # return wrap 給 func 物件
func()                      # 執行 func 物件


# 結果
# 'Code before executing func'
# 'This is a function!'
# 'Code After executing func'
```

# Syntax Candy
* 目的: 簡化語法
* 以'@'作為keyword  

舉例，同上
```python
# decorator function
def my_decorator(func):

    def wrap():
        print('Code before executing func')
        func()
        print('Code After executing func')
    return wrap     

# 需要裝飾的 func()
@my_decorator   # 等同 func = my_decorator(func) 
def func():         
    print('This is a function!')

# 執行
func()
```

# 觸發順序
* 觸發順序會由上往下

舉例
```python
def my_decorator_1(func):

    def wrap():
        print('1_before')
        func()
        print('1_after')
    return wrap     

def my_decorator_2(func):

    def wrap():
        print('2_before')
        func()
        print('2_after')
    return wrap     

# 順序為1先，2後
@my_decorator_1
@my_decorator_2
def func():         
    print('This is a function!')

func()
```
結果
```python
'1_before'
'2_before'
'This is a function!'
'2_after'
'1_after'
```

# Decorator副作用
* 裝飾詞在被 wrap 包一層後，其 \_\_name__ 屬性就會被修改成 wrap
```python
def my_decorator(func):

    def wrap():
        print('1_before')
        func()
        print('1_after')
    return wrap 

@my_decorator
def func():         
    print('This is a function!')

# __name__ 屬性被更改
print(func.__name__)
# 輸出 wrap
```
可以用 python 內建的 functools  

1. import wraps
2. 加上 @wraps(func)
```python
#1
from functools import wraps

def my_decorator(func):
    
    #2 多加一行
    @wraps(func)

    def wrap():
        print('1_before')
        func()
        print('1_after')
    return wrap 

@my_decorator
def func():         
    print('This is a function!')

print(func.__name__)
# 輸出 func
```

# 帶入參數
* 使用 *args, **kargs  
* 不定參數 * 代表以tuple方式引入， ** 代表以dict方式引入所以需要輸入key

舉例
```python
from functools import wraps

def my_decorator(func):
    
    @wraps(func)
    def wrap(*args, **kargs):           # 加入*args, **kargs
        print('This is a decorator')
        print('--------------')
        values = func(*args, **kargs)   # 存成物件
        return values                   # return 物件
    
    return wrap 


@my_decorator
def func(*args, **kargs):              
    print('This is a function!')
    print(args, kargs)

if __name__ == '__main__':
    func(1,2,b=3)

# 輸出
# This is a decorator
# --------------
# This is a function!
# (1, 2) {'b': 3}
```
**kargs 可以用來製作 dict 
```python
def dictFunc(**kargs):
    return kargs

print(dictFunc(a=1, b=2, c=3))
# {'a': '1', 'b': 2, 'c': 3}
```