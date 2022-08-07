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

# built-in word 'app_name'
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



# app_name, name_space
在許多 url 導到同一個 app 的情況下(減少重複原則)，在做 reverse() 的时候，就會混淆不知道要導回給誰。可以使用 namespace，在include函数中添加 namespace 即可。

```python
# project.urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('core1/', include('core.urls', namespace='core1')),
    path('core2/', include('core.urls', namespace='core2')),
]
```
```python
# app.urls.py

from django.urls import path
from .views import HomeView, ListView


app_name = 'core'
urlpatterns = [
    path('', HomeView, name='home'),
    path('list/', ListView, name='list')
]
```

```python
# views.py
# 這樣 reverse 就可以指定要給 core1:list 還是 core2:list

def back_to_list(request):
  return redirect(reverse('core1:list'))
```



