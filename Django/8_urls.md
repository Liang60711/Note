# Relative URL
使用 path() 函式的 name 參數來編輯 url
1. 設定 project/urls.py
2. 設定 app/urls.py
3. 帶入html   

### Relative URL 寫法
```html
<!--html-->
<!--引號內不能有空格-->
{% url '<app_name>:<url_name>' %}
```




project/urls.py
```python
from django.contrib import admin
from django.urls import path, include
from app import views

urlpatterns = [
    path('admin/', admin.site.urls),
    # 首頁
    path('',views.index,name='index'),
    # 分頁
    path('app/',include('app.urls')),
]
```
app/urls.py
```python
from django.urls import path
from app import views

# build-in word 'app_name'
app_name = 'app'

urlpatterns = [
    path('One/', views.One, name='PageOne'),
    path('Two/', views.Two, name='PageTwo'),
]
```
app/views.py
```python
from django.shortcuts import render

def One(request):
    return render(request, 'app/One.html')

def Two(request):
    return render(request, 'app/Two.html')
```
One.html
```html
  <body>
    <h1>This is One Page</h1>

    <!--寫法為 {% url '<app_name:<url_name>'%}     -->
    <a href="{% url 'app:PageTwo' %}">Link to Two</a>
  </body>
```

### Link to Admin
進行 migrate
```python
python manage.py migrate
```
先檢查 settings 中的 INSTALLED_APPS 有 admin，即可在 html 中編輯
```html
<a href="{% url 'admin:index' %}">Link to admin</a>
```