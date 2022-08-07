# CDN
* CDN link:  https://code.jquery.com/
# 查找 element 
* 從 $ 開始查找
* 回傳值型態為 **array**
### selector 語法和js的querySelector()相同
```javascript
$('#id')                // id查找
$('.class')             // className查找
$('[attr="value"]')     // attribute查找
```
### 舉例
```javascript
$('.container a')       // className 為 container 元素以下所有 a 連結

$('tr:first')           // 取得第一個tr元素

$('input[name="email"]')    // input中，name屬性為 email 的元素
```
### 回傳array長度
```javascript
$('#id').length     // js array 屬性取得
$('#id').size()     // jQuery object 方法取得
```


### 比較不同語法
```javascript
// jQuery
$('item')

// javascript
document.querySelector('#id')

// CSS
#id{
}
```

# jQuery Object
### 存成物件
```javascript
var X = $('<selector>')
```
用index查找jQuery Object
```javascript
X.eq(0)     // 指定第1個物件
X.eq(-1)    // 指定最末個物件
```

### 物件方法
X.css() 變更css
```javascript
X.css('color','red')    // 單property

X.css({'color':'red',
       'background':'black',
       'border':'white'
})                      // 多properties
```

X.text() 變更text
```javascript
X.text('new_text')
```

X.html() 變更html
```javascript
X.html('<em> new_html </em>')
```

X.attr() 變更html屬性
```javascript
$('input').eq(1).attr('type', 'checkbox')
// 將第2個input的type屬性改成checkbox
```
X.val() 變更value
```javascript
X.val('new_value')
```
**添加/移除/切換 屬性**
```html
<!--html-->
<style>
    h1{
        backgroundcolor: red;
        color: black;
    }
</style>
```
```javascript
// 添加property
X.addClass('h1')

// 移除property
X.removeClass('h1')

// 切換(開/關)property
X.toggle('h1')
```

# Event
* 可參閱API文件:https://api.jquery.com/category/events/
* 函數中的 this 為被觸發的 DOM元素，而非 jQuery 物件

建立Object，並使用Event
```javascript
$('h1').click(function(){
    $(this).text('new_text') 
})
```
