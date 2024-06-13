# computed 和 watch 比較

> 官網解釋 https://v2.cn.vuejs.org/v2/guide/computed.html#%E8%AE%A1%E7%AE%97%E5%B1%9E%E6%80%A7-vs-%E4%BE%A6%E5%90%AC%E5%B1%9E%E6%80%A7

## 結論

1. computed 能完成的功能，watch 都可以完成；watch 能完成的功能，computed 不一定能完成；例如 `watch 可以進行異步操作`。

2. 被 vue 所管理的函數，最好寫成普通函數，這樣 this 的指向才是 vm 或組件實例物件。

3. 所有不被 vue 所管理的函數，最好寫成箭頭函數，這樣 this 的指向才會是 vm 或組件實例物件。

    * 如 : setTimeout 的回調函數、ajax的回調函數、Promise回調函數。

<br/>

<br/>

## 舉例 - 情況1

需求 : fullName 需要依照 firstName 和 lastName 動態改變

`watch` 的寫法是`命令式且重複的`

```js
var vm = new Vue({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
    },
    watch: {
        firstName(val) {
            this.fullName = val + ' ' + this.lastName
        },
        lastName(val) {
            this.fullName = this.firstName + ' ' + val
        }
    }
})
```

`computed` 的寫法較為簡潔
```js
var vm = new Vue({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar'
    },
    computed: {
        fullName() {
            return this.firstName + ' ' + this.lastName
        }
    }
})
```

結論1: 當 computed 和 watch 都可以實現需求時，優先選擇簡潔的代碼，即使用 computed。


<br/>

## 舉例 - 情況2 需要異步操作時

需求 : fullName 需要依照 firstName 和 lastName 動態改變，`且延遲1秒後觸發`

`watch` 寫法，會成功

```js
var vm = new Vue({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
    },
    watch: {
        firstName(val) {
            setTimeout(() => {
                this.fullName = val + ' ' + this.lastName
            }, 1000)
        },
        lastName(val) {
            setTimeout(() => {
                this.fullName = this.firstName + ' ' + val
            }, 1000)
        }
    }
})
```

解析一下原因 : 

* 因為 setTimeout 函數並不是 vue 所管理的，是瀏覽器的 js 引擎。

* setTimeout 裡面`一定要寫成箭頭函數`，寫成箭頭函數後，內部的 this 會往外一層找到 firstName 函數，而 `firstName 函數的 this 是指向 vue 實例`，故 `this.fullName` 可以被執行。

    * ES6: 箭頭函數中的 this，是繼承外層作用域的 this。

* 呈上，若 setTimeout 中，寫成以下方式，會失敗

    ```js
    watch: {
        firstName(val) {
            setTimeout(function() {
                // this 為 Window，故以下 fullName 不存在
                this.fullName = val + ' ' + this.lastName
            }, 1000)
        }
    }
    ```

    * ES5: 非箭頭函數，哪個物件調用 setTimeout，誰就是 this，這邊是 js 引擎調用 setTimeout，所以 this 是 window。



`computed` 寫法，會失敗
```js
var vm = new Vue({
    el: '#demo',
    data: {
        firstName: 'Foo',
        lastName: 'Bar'
    },
    computed: {
        fullName() {
            setTimeout(() => {
                // 失敗的原因是這邊的return是返回給setTimeoput，並不是返回給 fullName，因此 fullName 為 undefined
                return this.firstName + ' ' + this.lastName
            }, 1000)
        }
    }
})
```

結論2 : 如果有異步需求，只能使用 watch，不能用 computed。
