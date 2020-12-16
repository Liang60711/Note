# CBViews
view 用 class 的方式來寫的話，可以繼承很多 django 內建的方法，節省時間
1. views.py中將函式寫成類別
2. 更改 urls.py<br>

<br>

### 舉例，View 物件
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

### 舉例，TemplateView 物件(可帶變數)
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