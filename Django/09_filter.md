# Template tags & filter
**Template tags**: 可以在 HTML 檔案裡使用類似 Python 的語法，存取從 views function 傳過來的變數。

**filter**: 在 html 頁面嵌入變數時，可增加**filter**或是**自訂函式**的功能。  
Django 內建的filter: https://docs.djangoproject.com/en/3.1/ref/templates/builtins/#built-in-filter-reference

* filter 用法:
```html
<!-- html -->

{{ <value> | <filter_func>:<arg> }}
```


1. 在 app 目錄下新增檔案，\_\_init__.py，一定要建立
    ```shell
    └─basicApp
        └─templatetags
            my_filters.py
            __init__.py
    ```
2. 編輯 my_filters.py檔案
3. 可在 html 中使用，記得 {% load %}


舉例一，使用內建 filter  

```html
<!-- html -->

{%extends 'basicApp/basic.html'%}

{%block content%}
<div class="jumbotron">
  
  <p>{{a | add:'b'}}</p>          <!-- 變數 a 加上 b，字串整數都可，與python相同 -->
  <p>{{b | cut:'hello'}}</p>      <!-- 變數 b，去除'hello'字串 -->
  <p>{{c | date:'Y/m/d'}}</p>     <!-- 年/月/日 格式化 -->

</div>
{%endblock%}
```

舉例二，自訂 filter 函數  
```python
# my_filters.py

from django import template
register = template.Library()


# 使用 decorators
@register.filter(name = 'plus')
def plus(value, arg):
    return str(value) + str(arg)
```
模板中加入變數
```html
<!-- html -->

<!DOCTYPE html>

{% extends 'basicApp/basic.html' %}
<!-- 記得把python檔 load進來，但要寫在{% extends %} 下面 -->
{% load my_filters %}
{%block content%}

<div class="jumbotron">

  <!-- 使用自訂 filter -->
  <h1>{{ a|plus:'apple' }}</h1>

</div>

{%endblock%}
```