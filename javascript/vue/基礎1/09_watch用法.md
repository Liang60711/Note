# watch 用法

## 概述

1. 用於監聽Vue實例中資料的變化，並在資料變化時執行對應的操作。

2. 監視屬性必須存在，才能監視

    * 除了監聽`屬性`(vm._data中的數據)之外，`也可以監聽 computed 中的計算屬性`。

    * 如果監視了一個不存在的屬性，console不會報錯，但也永遠不會觸發，等於是冗餘程式碼。

3. 當監視屬性發生變化時，handler 回調函數才會調用。

4. 監視的兩種寫法:

    1. 在 vm 實例中的 watch 屬性中配置。
    2. 通過 vm.$watch 配置。

<br/>

<br/>

## 舉例
監視 isHot 屬性變化
```js
const vm = new Vue({
    el: '#root',
    data: {
        isHot: true
    },
    watch: {
      isHot: { // key名稱為被監視的屬性
        immediate: true, // 初始化時，先調用一次handler，預設為false
        handler(newValue, oldValue) {
            // 當isHot發生改變時，handler會被調用
            console.log('isHot被修改了', newValue, oldValue)
        }
      }  
    },
    
})
```


<br/>

<br/>

## $watch 用法

除了使用 vm 實例中的 watch 配置監視功能，也可以在實例外使用 $watch 去外加監視功能，效果一樣。

```js
const vm = new Vue({
    el: '#root',
    data: {
        isHot: true
    }
})

// 使用 $watch 方法
// 參數1: 要監視哪個屬性(需用引號，原本的key值)
// 參數2: 配置物件(原本的value值)
vm.$watch('isHot', {
    immediate: true,
    deep: true,
    handler(newValue, oldValue) {
        console.log('isHot被修改了', newValue, oldValue)
    }
})
```

若要使用 $watch 的簡寫方式，將參數2直接寫成 handler 函數，同下方的簡寫方式

```js
// 注意，callback 函數不可寫成箭頭函數，否則this會指向window
vm.$watch('isHot', function(newValue, oldValue) {
    console.log('isHot被修改了', newValue, oldValue)
})
```


<br/>

<br/>

## 深度監視 - 用來監視物件中所有屬性的變化

### 結論
深度監視
1. 如果`_data屬性`為單層結構，則 watch 可以監視到數據變化；但如果是多層結構，watch 預設是監視不到數據的變化。

    * 舉例

        ```js
        // 單層
        data: {
            a: 1,
            b: 2
        }

        // 多層
        data: {
            numbers: {
                a: 1,
                b: 2
            }
        }
        ```
2. 對於多層結構，則需要使用 `deep: true` 配置，就可以監視多層結構中的數據變化。

    ```js
    const vm = new Vue({
        el: '#root',
        data: {
            numbers: {
                a: 1,
                b: 2
            }
        },
        watch: {
            numbers: {
                deep: true,
                handler() {
                    console.log('a或b發生改變')
                }
            }
        }
    })
    ```

備註: 

1. vm 自身是可以監測到物件內部值的改變，但 vue 提供的 watch 預設不行 (因為deep: false)。

2. 使用 watch 時，應根據數據結構，決定是否要使用深度監視。

3. 預設不開啟深度監視，是為了效能考量。


<br/>

<br/>

### 範例說明

data 中有一個物件 numbers

假設只需要監視 `numbers.a` 屬性，監視的 key 應該要寫成 `'numbers.a'`
```js
const vm = new Vue({
    el: '#root',
    data: {
        numbers: {
            a: 1,
            b: 1
        }
    },
    watch: {
        'numbers.a': {
            handler() {
                console.log('a被改變了')
            }
        }
    }
})
```

但如果需求是，需要監測 numbers 中每一個屬性 a, b，此時寫成以下，不會有監視效果

```js
const vm = new Vue({
    el: '#root',
    data: {
        numbers: {
            a: 1,
            b: 1
        }
    },
    watch: {
        // 若寫成 numbers，handler不會觸發
        numbers: {
            handler() {
                console.log('改變了')
            }
        }
    }
})
```

<br/>

原因如下

假設 numbers 物件的記憶體位址為 `0x123`，當 `numbers.a` 或 `numbers.b` 屬性發生改變時，記憶體位址並不會發生改變，`vue 會認為 numbers 的記憶體位址沒有改變，所以不會觸發 handler 回調函數`。

解決方式

使用深度監視，不只觀察 numbers 記憶體位址，而是直接去監視 numbers 中的值是否有改變，可以用以下配置

```js
const vm = new Vue({
    el: '#root',
    data: {
        numbers: {
            a: 1,
            b: 1
        }
    },
    watch: {
        // 若寫成 numbers，不會有 watch 效果產生
        numbers: {
            deep: true, // 深度監視，預設為 false
            handler() {
                console.log('改變了')
            }
        }
    }
})
```

<br/>

<br/>

## watch 簡寫方式

簡寫的條件，當不需要使用額外的配置，如

* `immediate: true` 初始化後，立即觸發一次 handler 函數

* `deep: true` 開啟深度監視

`不使用以上配置，才能使用簡寫`

```js
const vm = new Vue({
    el: '#root',
    data: {
        isHot: true
    }, 
    watch: {
        // 正常寫法
        // isHot: {
        //     immediate: true,
        //     deep: true,
        //     handler(newValue, oldValue) {
        //         console.log('isHot被修改了', newValue, oldValue)
        //     }
        // }

        // 簡寫 
        isHot(newValue, oldValue) {
            console.log('isHot被修改了', newValue, oldValue)
        }
    }
})
```