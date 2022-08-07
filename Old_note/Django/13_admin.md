# Admin actions 功能
預設的 action 只有 delete selected objects，添加按照以下步驟  

1. 定義一個 action 功能函式
2. 設定 action 顯示名稱
3. 加入到 model 中

```python
# models.py 
class Order(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL,
                            on_delete=models.CASCADE)
    ref_code = models.CharField(max_length=20)
    items = models.ManyToManyField(OrderItem)
    start_date = models.DateTimeField(auto_now_add=True)
    ordered_date = models.DateTimeField()

    refund_requested = models.BooleanField(default=False)   # 退單需求
    refund_granted = models.BooleanField(default=False)     # 被核可的退單
```
```python
# admin.py

from django.contrib import admin
from .models import Order

#1 定義 action 功能函式
def make_refund_accepted(modeladmin, request, queryset):
    queryset.update(refund_granted=True)

#2 設定 action 顯示名稱
make_refund_accepted.short_description = 'Update orders to refund granted'


# 定義一個狀態欄
class OrderAdmin(admin.ModelAdmin):
    # 更改 fields順序
    fields = [
        'ref_code',
        'user', 
        'refund_requested',
        'refund_granted',
    ]
    
    # 狀態欄要顯示的 columns
    list_display = [
        'user', 
        'ref_code',
        'refund_requested',
        'refund_granted',
    ]
    
    # 狀態欄 columns link (必須在 list_display 裡面)
    list_display_links = [
        'ref_code',
    ]
    
    # 右方篩選欄
    list_filter = [
        'refund_requested',
        'refund_granted',
    ]

    # 上方 search bar 
    search_fields = [
        'user__username',
        'ref_code',
    ]

    # List View 可以直接手動更改
    list_editable = [
        'red_code',
    ]

    #3 將功能函式 加入到 model 中
    actions = [make_refund_accepted]

#4 登記 model
admin.site.register(Order, OrderAdmin)
```