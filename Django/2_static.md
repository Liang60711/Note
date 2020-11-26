# Static Files 靜態文件
* 包含 圖片、JavaScript、CSS 檔案等靜態文件，
* 靜態文件可提供重複性高的檔案使用

1. 新增目錄: project主目錄 > static > images
2. 修改 settings.py設定檔
3. 修改需加入靜態文件的 html 檔案 

修改 settings.py設定檔
```python
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')
# 新增static file的路徑
STATIC_DIR = os.path.join(BASE_DIR, 'static')


# url位置
STATIC_URL = '/static/'    
# 新增靜態文件的路徑list
STATICFILES_DIRS = [
    STATIC_DIR,
]
```
修改html檔案，使用 {% static <img_path> %}
```html
<!DOCTYPE html>

<!--載入靜態文件-->
{% load static %}

<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Django image page</title>
  </head>
  <body>
    <h1>this is a picture</h1>
    
    <!--使用{% static %} 插入文件-->
    <img src="{% static 'images/sky.jpg' %}" alt="didnt show">
  </body>
</html>
```
舉例，再加入CSS檔案
```css
/*css file*/

h1{
    color:red;
}

body{
    background:##fff8a8;
}
```
修改html
```html
<!DOCTYPE html>

<!--載入靜態文件-->
{% load static %}

<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    
    <!--link css file-->
    <link rel="stylesheet" href="{%static 'css/style.css'%}">
    
    <title>Django image page</title>
  </head>
  <body>
    <h1>this is a picture</h1>
    
    <!--load img file-->
    <img src="{% static 'images/sky.jpg' %}" alt="didnt show">
  </body>
</html>
```