# Templates

## 設定templates路徑
1. 在project目錄下新增 templates 目錄
2. cd project.project，設定 settings.py檔案
3. 編輯 templates 中 html檔案

```python
└─project
    │  db.sqlite3
    │  manage.py
    │
    ├─AppTwo
    │  │  admin.py
    │  │  apps.py
    │  │  models.py
    │  │  tests.py
    │  │  urls.py
    │  │  views.py
    │  │  __init__.py
    │  │
    │  ├─migrations
    │
    ├─project
    │  │  asgi.py
    │  │  settings.py      #2 設定
    │  │  urls.py
    │  │  wsgi.py
    │  │  __init__.py
    │
    └─templates            #1 建立目錄
       ├─AppTwo  
         │  index.html     #3 編輯html

```
settings.py檔案
```python
import os
from pathlib import Path

# __file__ 指的是settings.py
BASE_DIR = Path(__file__).resolve().parent.parent


# 設定TEMPLATES 的 'DIRS' 即可
TEMPLATES = [
    {
        # 模板引擎，也可以換成 django.template.backends.jinja2.Jinja2
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # 設定模板位置
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        # 改成 True 會先從 App資料夾中找 templates 資料夾，沒找到才會找 project 資料夾下的 templates
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

## 在 templates 中顯示變數
使用 {{variable_name}}，把變數丟在雙花括號內

```python
<h1><em> {{variable_name}} </em></h1>
```

## render function
* render(request, template_name, dictionary)
* **request**: HttpRequest 物件
* **template_name**: 傳遞給哪個 template
* **dictionary**: 包含要新增至 template 的變數，dict型態

舉例，render() 將變數傳遞給 templates
```python
# views.py
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    # 變數傳送型態為dict
    my_dict = {'insert_me':'Hello I am from views.py'}
    # render 渲染函式 將變數傳遞給AppTwo/index.html
    # template_name 固定在 templates 目錄之下
    render(request, 'AppTwo/index.html', context=my_dict)
```

```html
# templates > index.html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>first app</title>
  </head>
  <body>
      <h1>Hello this is index.html</h1>
      {{insert_me}}     <!--變數-->
  </body>
</html>
```

<br>

# Templates Inheritance 模板繼承
為了減少 html 重複性，可設立一個 base.html 製作相同的地方，再留自定義區域  
1. 設定 app 中的 urls.py檔案
2. 撰寫一個 base.html
3. 再其他頁面導入模板，並撰寫其他內容


自定義語法:
```html
<!-- html -->

{% block <BLOCKNAME> %}
{% endblock %}

<!-- <BLOCKNAME> 不是保留字，可隨意定義 -->
```
導入語法:
```html
{% extends '<TEMPLATE_NAME>' %}

<!--用上面 tree 舉例-->
{% extends 'basicApp/base.html' %}
```

舉例:
```shell
└─basicApp
    urls.py

└─templates
    └─basicApp
            base.html
            index.html
            one.html
            two.html
```
1.設定 basicApp/urls.py
```python
from basicApp import views
from django.urls import path

app_name = 'basicApp'
urlpatterns = [
  path('one/', views.one , name = 'one'),
  path('two/', views.one , name = 'two'),
]
```


2.先寫 base.html 作為模板
```html
<!-- 以 Bootstrap v4.5 為例 -->

  <body>
    <nav class='navbar navbar-expand-lg navbar-light bg-light'>
        <a class='navbar-brand' href="{% url 'index' %}">Index</a>
        
        <ul class='nav'>
          <!-- href 帶入 -->
          <li class='nav-item'><a class='nav-link' href="{% url 'basicApp:one'%}">user_list</a></li>
          <li class='nav-item'><a class='nav-link' href="{% url 'basicApp:two'%}">user_add</a></li>
          <li class='nav-item'><a class='nav-link' href="{% url 'admin:index'%}">admin</a></li>
        </ul>

    </nav>

    <div class="container">
      <!-- 此為自定義的區塊 -->
      {% block content %}
      {% endblock %}
    </div>

  </body>
```
index.html 中導入模板，並編輯自定義區塊
```html
<!DOCTYPE html>
<!-- 導入模板 -->
{% extends 'basicApp/base.html' %}
  
  <!-- 以下為自定義區塊 -->
  {% block content %}
    <h1>this is index page</h1>
  {% endblock %}
```

