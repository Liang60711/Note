# 綁定自訂 style

## 觀念

* 同class，不須親自操作 DOM，只需要修改屬性，由 vue 去操作管理 DOM。


<br/>

<br/>

## 範例1 - 綁定style樣式(Object)

模板，此時 `:style`後就變為 js 表達式。

```html
<div id="root">
    <div :style="styleObj"> {{ name }} </div>
</div>
```

vue 語法，styleObj 物件的 key 不能亂寫，必須是 css 中的寫法。

```javascript
const vm = new Vue({
    el: '#root',
    data: {
        name: 'apple',
        styleObj: {
            fontSize: '40px',
            color: 'red',
            backgroundColor
        }
    }
})
```

<br/>

<br/>


## 範例2 - 綁定style樣式(Array)

其實不太常用，但可以過。

模板，使用 `[]` ，可寫多個物件在裡面。

```html
<div id="root">
    <div :style="[styleObj1, styleObj2]"> {{ name }} </div>
</div>
```

vue 語法，styleObj 物件的 key 不能亂寫，必須是 css 中的寫法。

```javascript
const vm = new Vue({
    el: '#root',
    data: {
        name: 'apple',
        styleObj1: {
            fontSize: '40px'
        },
        styleObj2: {
            color: 'red',
            backgroundColor
        },
    }
})
```

<br/>

<br/>
