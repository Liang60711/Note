# Forms 表單
* 建立互動式表單
* HTTP 傳送 form 有 get / post 兩種方式
* get 參數直接顯示在 URL
* post 會有 csrf_token，否則無法傳送

## 建立 forms
1. 在 App 目錄下新建立 forms.py檔案
2. 在 App.views 檔案中編輯 views
3. 編輯 templates 中的 html

<br>

forms.py檔案
```python
#1 
from django import forms

#2 和寫 models.Model 類似
class Form_Test(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)
```
views.py檔案
```python
from . import forms

# get method
def form_get(request):
    form = forms.Form_Test()    # 將 class 存成物件
    return render(request, 'App/form.html')

# post method
def form_post(request):
    
    # 如果是 post
    if request.method == 'POST':
        form = forms.Form_Test(request.POST)

        if form.is_valid():
            # 如果驗證成功則會產生一個dict，cleaned_data
            print(form.cleaned_data)
            print('Name:',form.cleaned_data['name'])
            print('Email:',form.cleaned_data['email'])
            print('Text:',form.cleaned_data['text'])
    
    # 如果是 get
    else:
        form = forms.FormName()
    

    return render(request, 'App/form.html',{'form':form})
```

html 檔案
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    
    <!-- Bootstrap CDN -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">

    <title>forms</title>
  </head>
  <body>
    
    <div class="container">
      
      <form  method="post">
        {{form.as_p}}
        {% csrf_token %}        <!-- post 一定要有 csrf-->
        
        <input type="submit" class='btn btn-primary' value="sumbit">

      </form>
    </div>
  </body>
</html>
```
form.as_p 可以將 form 以 html 的 \<p\> 來裝飾。  
form.as_table 為 \<tr\>，form.as_ul 為 \<li\>。

* form.as_p
* form.as_table
* form.as_ul


## 資料驗證
以檢驗機器人為例，html 中 input 的value 屬性有值，代表是機器人，django 提供幾種資料驗證方式:
1. clean_ + 分類屬性 為 函式名稱
2. validators method
3. 客製化驗證  

<br>

### 第一種: clean_ + 分類屬性 為 函式名稱
```python
# forms.py

from django import forms

class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)

    # 新增一個類別屬性
    # required 是否為必填，預設為 True
    # widget 設為隱藏
    botcatcher = forms.CharField(required=False,
                                 widget=forms.HiddenInput)

    # 新增一個類別方法，以 clean_ 開頭
    def clean_botcatcher(self):
        botcatcher = self.cleaned_data['botcatcher']

        # 若 botcatcher 有值，則 raise 錯誤
        if len(botcatcher) > 0:
            raise forms.ValidationError("Gotcha bot!!!")

        return botcatcher
```

### 第二種: validators，更簡潔
```python
# forms.py

from django import forms
# 匯入
from django.core import validators

class FormName(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)

    # 只需加一個參數
    botcatcher = forms.CharField(required=False,
                                 widget=forms.HiddenInput,
                                 validators=[validators.MaxLengthValidator(0)])
```
validators  
方法參考: https://docs.djangoproject.com/en/3.1/ref/validators/
```python
# list 裡面可放 函式 或 可以__call__的類別(class)
validators = [<method>,]

# 舉例，長度超過 limit 值報錯
validators.MaxLengthValidator(0)

# 舉例，格式非 email 報錯
validators.EmailValidator()
```
### 第三種: 客製化驗證   

表單中，name 開頭必須要為 z 為例： 
利用 validators 客製化資料驗證，新增一個 method 來寫入驗證邏輯，並加到屬性參數中。
```python
from django import forms
# 匯入
from django.core import validators


# 新增 method
def check_for_z(value):
    if value[0].lower() != 'z':
        raise forms.ValidationError('NAME NEEDS TO START WITH Z')


class FormName(forms.Form):
    # 加到 name 參數中
    name = forms.CharField(validators=[check_for_z])
    email = forms.EmailField()
    text = forms.CharField(widget=forms.Textarea)

    botcatcher = forms.CharField(required=False,
                                 widget=forms.HiddenInput,
                                 validators=[validators.MaxLengthValidator(0)])
```

### 舉例，再次輸入 email 欄位驗證是否相同
```python
from django import forms
from django.core import validators


class FormName(forms.Form):
    name = forms.CharField(validators=[check_for_z])
    email = forms.EmailField()

    # 新增欄位
    email_repeat = forms.EmailField(label='Enter your email again')

    text = forms.CharField(widget=forms.Textarea)

    # 新增方法
    # super().clean() 會驗證所有的欄位，並將乾淨的欄位回傳成dict型態 (和clean_data 相同)，為固定用法
    def clean(self):
        all_clean_data = super().clean()
        email = all_clean_data['email']
        email_repeat = all_clean_data['email_repeat']

        if email != email_repeat:
            raise forms.ValidationError('MAKE SURE EMAIL MATCH')
```

<br>

### super() 函式 
當子類別中定義了與父類別同名的方法(Method)，這時候子類別的物件(Object)呼叫這個同名方法時，其中會覆蓋掉父類別的同名方法：
```python
# 父類別
class Parent():
    def hello(self):
        print('Hi I am parent hello')


# 子類別
class Child(parent):
    def hello(self):
        print('Here is child')


if __name__=='__main__':
    aaa = Child()
    aaa.hello()

    # 只輸出 Here is child ，父類別被覆蓋掉
```
如果改成
```python
# 父類別
class Parent():
    def hello(self):
        print('Hi I am parent hello')


# 子類別
class Child(parent):
    def hello(self):
        # super()
        super().hello()
        print('Here is child')


if __name__=='__main__':
    aaa = Child()
    aaa.hello()

    # 輸出 
    # Hi I am parent hello
    # Here is child
```
子類別透過 super() 方法執行父類別的 hello()方法後，接著執行子類別的後續實作。