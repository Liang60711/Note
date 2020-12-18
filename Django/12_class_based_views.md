# CBViews
view 用 class 的方式來寫的話，可以繼承很多 django 內建的方法，節省時間
1. views.py中將函式寫成類別
2. 更改 urls.py<br>

<br>

# View object
```python
# views

from django.shortcuts import render
from django.views.generic import View
from django.http import HttpResponse

# class 名稱自訂，但要繼承 class View
class CBView(View):
    # get 是 built-in
    def get(self, request):
        return HttpResponse('This is CBViews!')
```
```python
# urls.py

from django.contrib import admin
from django.urls import path
from basicApp import views

urlpatterns = [
    # as_view()
    path('', views.CBView.as_view()),
    path('admin/', admin.site.urls),
]
```

# TemplateView object(可帶變數)
```python
# views

from django.shortcuts import render
from django.views.generic import View, TemplateView # TemplateView
from django.http import HttpResponse

# 只有 class 名稱自定義
class IndexView(TemplateView):
    # built-in
    template_name = 'basicApp/index.html'

    # 新增變數
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['one'] = 'THIS IS VARIATION'
        return context
```
```python
# urls.py

from django.contrib import admin
from django.urls import path
from basicApp import views

urlpatterns = [
    # as_view()
    path('', views.IndexView.as_view()),
    path('admin/', admin.site.urls),
]
```
```html
<!-- html (base.html 省略)-->

{% extends 'base.html' %}

{% block content %}

<h1>hello world index home page</h1>
<h2>content: {{one}}</h2>

{% endblock %}
```

# ListView and DetailView
```python
# models.py

from django.db import models

class School(models.Model):
    name = models.CharField(max_length=256)
    principal = models.CharField(max_length=256)
    location = models.CharField(max_length=256)

    def __str__(self):
        return self.name

class Student(models.Model):
    name = models.CharField(max_length=256)
    age = models.PositiveIntegerField()
    # School 和 Student 關係為一對多
    school = models.ForeignKey(School, related_name='student', on_delete=models.CASCADE)

    def __str__(self):
        return self.name
```

```python
# views.py

from django.shortcuts import render
from django.views.generic import TemplateView, ListView, DetailView
from . import models

class IndexView(TemplateView):
    template_name = 'index.html'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['one'] = 'BASEIC ONE'
        return context


class SchoolListView(ListView):
    # 定義在html中 list的名稱，若不指定名稱，則預設為小寫 model + _list，即 school_list
    context_object_name = 'school_list'
    
    # built-in
    model = models.School


class SchoolDetailView(DetailView):
    # 若不指定名稱，則預設為小寫 model 名稱，即 school
    context_object_name = 'school_detail'

    # built-in
    model = models.School
    template_name = 'basicApp/school_detail.html'
```
```python
# project/urls.py

from django.contrib import admin
from django.urls import path,include
from basicApp import views

urlpatterns = [
    path('', views.IndexView.as_view()),
    path('admin/', admin.site.urls),
    path('basicApp/', include('basicApp.urls',namespace='basicApp')),
]
```
```python
# basicApp/urls.py

from django.urls import path
from basicApp import views

app_name = 'basicApp'
urlpatterns = [
    path('', views.SchoolListView.as_view(), name='list'),
    path('<pk>/',views.SchoolDetailView.as_view(),name='detail'),
]

```
```html
 <!-- basicApp_base.html -->

<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
    <title>Base</title>
  </head>
  <body>
    <nav class='navbar navbar-light bg-light'>
    <div class="container">
      <ul class='nav'>
        <li class='nav-brand'><a class='nav-link' href="{%url 'basicApp:list'%}">Schools</a></li>
        <li class='nav-item'><a class='nav-link' href="{%url 'admin:index'%}">Admin</a></li>
        <li class='nav-item'><a class='nav-link' href="#"></a></li>
      </ul>
    </div>
    </nav>

    <div class="container">
      {% block content %}
      {% endblock %}
    </div>
  </body>
</html>
```

```html
<!-- school_list.html -->

{%extends 'basicApp/basicApp_base.html'%}

{% block content %}
<h1>welcome to a list of all the schools</h1>
<ol>
  {% for s in school_list %}
  <!-- 連結網址為 PrimaryKey -->
  <h2><li><a href="{{s.id}}">{{s.name}}</a></li></h2>
  {% endfor %}
</ol>

{% endblock %}
```

```html
<!-- school_detail -->

```