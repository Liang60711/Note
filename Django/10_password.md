# Hash
雜湊(Hash)演算法原先是一種用在資料編碼中的技術。與加密的區別是，加密有密鑰，意思是加密可逆，雜湊不可逆。

特性:
1. 原文的內容長短，運算完的輸出都會是固定的長度。
2. 雜湊演算法的輸出被稱做 Hash Value。
3. 兩個輸入的內容即便只差一個字，產生的輸出內容卻會差很多。
4. 相同的內容輸入，得到的輸出必定一樣。
5. 無法將輸出解回原本的輸入，hash 是單向的。
6. salt: 基本上就是額外加入的字串，就算原始輸入被硬解出來，也因為加了 salt 而不是原始的輸入，安全性相對增加。

# SHA-256
雜湊演算法的一種，輸出大小為 256 bits。

<br>


# 密碼管理
1. 套件安裝
2. 建立 media 目錄
3. 設定 settings.py<br>
<br>

###  1.密碼管理套件 安裝
```python
pip install bcrypt
```

```python
pip install django[argon2]
```
### 2. 建立 media 目錄 (與 templates 同層級) 
* media 與 static 的不同在於，前者是 user 給的靜態文件，如註冊照片；後者是開發者給的。
```python
# 建立好後，來到 settings.py

import os
MEDIA_DIR = os.path.join(BASE_DIR, 'media')

# MEDIA
MEDIA_URL = '/media/'
MEDIA_ROOT = MEDIA_DIR
```

### 3.設定 settings.py<br>
* List 的第一個元素，即 PASSWORD_HASHERS[0] 會用於儲存密碼，其它元素用於驗證雜湊值。如果打算使用不同的演算法，則需修改 PASSWORD_HASHERS，將演算法放在列表中首位。

PASSWORD_HASHERS
```python
# Password validation
# https://docs.djangoproject.com/en/3.1/ref/settings/#auth-password-validators

PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.BCryptPasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
]
```

AUTH_PASSWORD_VALIDATORS
* **UserAttributeSimilarityValidator**: 驗證密碼是否與用戶的某些屬性有很大的區別
* **MinimumLengthValidator**: 驗證密碼是否符合最小長度，最短為9碼

* **CommonPasswordValidator**: 檢查密碼是否在常用密碼列表中，如ABCDEFGH(有內建常用密碼，預設與2000個常用密碼比較)
* **NumericPasswordValidator**: 檢查密碼是否完全是數字
```python
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        # 可新增參數
        'OPTIONS': {
            'min_length':9
        }
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```


# 使用者登入功能 及 User Model
**User Model**: Django 的使用者登入系統中，已經有使用者資訊的 model，不用再自行建立 model。而 User object 有五個預設欄位，至於其他要添加的欄位需要另行填寫:
1. username
2. password
3. email
4. first_name
5. last_name  

舉例，建立一個登入介面  
1. models.py編輯 model
2. admin.py中 register model
3. forms.py產生表格
4. views.py編輯
5. 記得要 makemigrations, migrate

### 1.編輯 models
```python
# models.py


from django.db import models
# built-in User object
from django.contrib.auth.models import User

# create model
class UserProfile(models.Model):
    # 使用 User object 繼承五個欄位
    # 通常登入系統是一對一關係，一個 User 只有一個 UserProfile，反之亦然
    user = models.OneToOneField(User, on_delete=models.CASCADE)

    # 自定義欄位
    portfolio = models.URLField(blank=True)
    # upload_to='profile_pics' 表示檔案位置儲存在 media/profile_pics/ 內
    picture = models.ImageField(upload_to='profile_pics', blank=True)

    def __str__(self):
        # user 繼承 User object 的5個屬性
        return self.user.username
```

### 2.Register
```python
# admin.py

from django.contrib import admin
from app.models import UserProfile

admin.site.register(UserProfile)
```

### 3.產生 form
```python
# forms.py

from django import forms
from django.contrib.auth.models import User
from basicApp.models import UserProfile


class UserForm(forms.ModelForm):
    # 將原有 User 的屬性覆蓋掉，User 內的 password 欄位字體沒有隱藏
    password = forms.CharField(widget=forms.PasswordInput())

    class Meta():
        model = User
        fields = ['username', 'email', 'password']
        # 也可以用 exclude
        # exclude = ['first_name', 'last_name']
        
        widgets = {
            'username': forms.TextInput(attrs={'class': 'form-control'}),
            'email': forms.EmailInput(attrs={'class': 'form-control'}),
            'password': forms.PasswordInput(attrs={'class':'form-control'})
        }


class UserProfileForm(forms.ModelForm):
    class Meta():
        model = UserProfile
        fields = ['portfolio','picture']
        widgets={
            'portfolio':forms.URLInput(attrs={'class':'form-control'}),
        }
```
### 4. 編輯 view
```python
# views.py

from django.shortcuts import render
from basicApp.forms import UserForm, UserProfileForm

# 首頁
def index(request):
    return render(request, 'basicApp/index.html')

# 註冊
def register(request):
    # 未註冊過 = False
    registered = False

    if request.method == 'POST':
        userform = UserForm(request.POST)
        userprofileform = UserProfileForm(request.POST)

        if userform.is_valid() and userprofileform.is_valid():
            # userform 部分
            user = userform.save(commit=False)
            # hash 密碼 
            user.set_password(user.password)
            user.save()


            # userprofileform 部分
            profile = userprofileform.save(commit=False)
            # 設定 UserProfile_Form.user 和 UserForm.user 的一對一關係
            profile.user = user
            # 檢查有沒有圖
            if 'picture' in request.FILES:
                print('found pic')
                profile.picture = request.FILES['picture']
            profile.save()
            registered = True
        else:
            print(userform.errors,userprofileform.errors)
    else:
        userform = UserForm()
        userprofileform = UserProfileForm()
    register_dict = {'registered':registered, 'userform':userform, 'userprofileform':userprofileform}
    return render(request, 'basicApp/register.html',register_dict)

```