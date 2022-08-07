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

{% extends 'basicApp/basicApp_base.html'%}

{% block content %}

<div class="jumbotron">
  <h1>school detail page</h1>
  <h2>school details:</h2>
  <p>Name: {{school_detail.name}}</p>
  <p>Principal: {{school_detail.principal}}</p>
  <p>Location: {{school_detail.location}}</p>
  <h3>Students: </h3>
  {% for s in school_detail.student.all %}

  <p>{{s.name}} who is {{s.age}} years old.</p>
  {% endfor %}
</div>

{% endblock %}

```
# CRUD View
* 透過 View 直接從 Model 直接新增(修改、刪除) Form，不必透過 ModelForm 來新增。
* 在 template 中，都以 \<form\> 來傳遞資料。


# CreateView
1. model.py<br>
2. views.py<br>
3. url.py<br>
4. html

### 1.model.py編輯: 在資料進行驗證、成功並儲存了之後，必須要設定要轉到哪一個網址，所以在 model 必須定義 get_absolute_url(self)
```python
# models.py
from django.db import models

class School(models.Model):
    name = models.CharField(max_length=256)
    principal = models.CharField(max_length=256)
    location = models.CharField(max_length=256)
    def __str__(self):
        return self.name
    
    # html 中 post form 之後，須轉往哪個網址
    def get_absolute_url(self):
        return reverse('basicApp:detail', kwargs={'pk':self.pk})
```
### 2. views 撰寫 CreateView
```python
# views.py
from django.urls import reverse, reverse_lazy
from django.views.generic import CreateView


# CreateView
class SchoolCreateView(CreateView):
  model = School
  fields = ['name', 'principal', 'location']

  # template 預設名稱為 小寫 model + _form，即 school_form.html，可改成別的
  template_name = 'school_form.html'

  # 若 model 沒定義 get_absolute_url(self)，可在這邊加寫
  # reverse_lazy 會等程式都跑完，才會 redirect 到該頁面
  success_url = reverse_lazy('basicApp:list')

# UpdateView
class SchoolUpdateView(UpdateView):
    model = School
    fields = ['name', 'principal']
    template_name = 'school_form.html'

# DeleteView
class SchoolDeleteView(DeleteView):
    model = School
    success_url = reverse_lazy('basicApp:list')
    # delete 確認頁
    template_name = 'school_delete.html'

# ListView
class SchoolListView(ListView):
    template_name = 'list.html'
    model = School
    context_object_name = 'school_list'

# DetailView
class SchoolDetailView(DetailView):
    template_name = 'detail.html'
    model = School
    context_object_name = 'school_detail'
```
### 3. urls.py編輯網址
```python
# urls.py

from django.urls import path
from basicApp import views
from basicApp.models import School

app_name = 'basicApp'
urlpatterns = [
  path('list/', views.SchoolListView.as_view(), name='list'),
  path('list/<pk>/', views.SchoolDetailView.as_view(), name='detail'),
  path('create/', views.SchoolCreateView.as_view(), name='create'),
  path('update/<pk>/', views.SchoolUpdateView.as_view(), name='update'),
  path('delete/<pk>/', views.SchoolDeleteView.as_view(), name='delete'),
]
```

### 4. hmtl
```html
<!-- basicApp_base.html -->

<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
    <title></title>
  </head>
  <body>
    <nav class='navbar navbar-light bg-light'>
    <div class="container">
    <ul class='nav'>
        <a class='navbar-brand' href="{% url 'index' %}">Index</a>

        <li class='nav-item'>
        <a class='nav-link' href="{% url 'admin:index'%}">Admin</a>
        </li>

        
        {% if user.is_authenticated %}
        <li class='nav-item'>
        <a class='nav-link' href="{% url 'basicApp:list'%}">List</a>
        </li>
        <li class='nav-item'>
        <a class='nav-link' href="{% url 'basicApp:user_logout'%}">Logout</a>
        </li>
        {% else %}
        <li class='nav-item'>
        <a class='nav-link' href="{% url 'basicApp:register'%}">Register</a>
        </li>
        <li class='nav-item'>
        <a class='nav-link' href="{% url 'basicApp:user_login'%}">Login</a>
        </li>
        {% endif %}

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
<!-- list.html -->

{%extends 'basicApp_base.html'%}
{% block content %}

<div class="jumbotron">
<h1>welcome to a list of all the schools</h1>
<ol>
  {% for s in school_list %}
  <h2><li><a href="{{s.id}}">{{s.name}}</a></li></h2>
  {% endfor %}
</ol>

</div>
<!-- 新增 Create 連結 -->
<a class='btn btn-primary' href="{% url 'basicApp:create'%}">Create New School</a>

{% endblock %}
```
```html
<!-- detail.html -->

{% extends 'basicApp_base.html' %}
{% block content %}

<div class="jumbotron">
 <p>Name: {{school_detail.name}}</p>
 <p>Pricipal: {{school_detail.principal}}</p>
 <p>Location: {{school_detail.location}}</p>

 {% for s in school_detail.student.all %}
 <p>Name: {{s.name}}</p>
 <p>Age: {{s.age}}</p>
 {% endfor %}

</div>

<!-- 新增 Update 和 Delete 連結 -->
<!-- 傳遞 pk 參數 -->
<div>
  <a class='btn btn-warning' href="{% url 'basicApp:update' pk=school_detail.pk %}">Update</a>
  <a class='btn btn-danger' href="{% url 'basicApp:delete' pk=school_detail.pk %}">Delete</a>
</div>

{% endblock %}
```

```html
<!-- school_form.html -->

{% extends 'basicApp_base.html'%}
{% block content %}
<h1>
  <!-- 如果 form(built-in) 的 primarykey 不存在  -->
  {% if not form.instance.pk %}
  Create School
  {% else %}
  Update School
  {% endif %}

</h1>

<form method="post">
  {% csrf_token %}
  {{form.as_p}}
  <input class='btn btn-primary' type="submit" value="Submit">
</form>


{% endblock %}
```
```html
<!-- school_delete.html(delete 確認頁) -->

{% extends 'basicApp_base.html'%}
{% block content %}
<h1>Delete {{school.name}}?</h1>

<form method="post">
  {% csrf_token %}
  <!-- Delete -->
  <input class='btn btn-danger' type="submit" value="Delete">
  <!-- Cancel -->
  <a href="{% url 'basicApp:detail' pk=school.pk %}">Cancel</a>
</form>

{% endblock %}
```