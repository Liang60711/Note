# 綁定自訂 class

## 觀念

* 不須親自操作 DOM，只需要修改`_data屬性`，由 vue 去操作管理 DOM。

    * 這就體現了vue的威力，只需要修改`_data屬性`就好，快速簡潔。


<br/>

<br/>

## 範例 - 綁定class樣式

基本的 class 使用原生語法，需要做改變的 class 使用 vue 語法，`v-bind:class`，簡寫成 `:class`
```html
<!-- 自訂class樣式 -->
<style>
    .normal-1{
        /*...*/
    }
    .normal-2{
        /*...*/
    }
</style>

<div id="root">
    <!-- 此種方式適用於 : class類名不確定，需要動態指定 -->
    <div class="basic" :class="aaa" @click="changeClass">{{name}}</div>
</div>

    <!-- 此種方式適用於 : 陣列寫法，要綁定的樣式個數不確定、名字也不確定 -->
    <div class="basic" :class="classArr">{{name}}</div>
</div>

    <!-- 此種方式適用於 : 物件寫法，要綁定的樣式個數確定、名字確定、但要動態決定用不用 -->
    <div class="basic" :class="classObj">{{name}}</div>
</div>
```

```js
const vm = new Vue({
    el: '#root',
    data: {
        name: '自訂class',
        aaa: 'normal-1',
        classArr: ['normal-1','normal-2','normal-3'],
        classObj: {
            'normal-1': false,
            'normal-2': true,
            'normal-3': false,
        }
    }, 
    methods: {
        changeClass() {
            this.aaa = 'normal-2'
        }
    }
})
```

最終，vue 會顯示 `class="basic normal"`