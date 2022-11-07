## this
`this`，可以依照執行情況，代表以下3種不同的意思
1. 直接執行: window (global)
2. 作為物件的成員函式執行: 該物件
3. 作為 dom 的監聽函式執行: 該 dom

    ```js
    let name = 'hello window';
    let getName = function() {
        console.log(this.name); // this在不同情況下執行會指向不同的物件
    }

    // 1. 直接執行
    getName(); // hello window

    // 2. 作為物件成員函式執行
    teacher = {
        name: "hello object"
    }
    teacher.getName = getName
    teacher.getName(); // hello object

    // 3.作為dom的監聽函式執行
    // <button id="btn" name="hello dom">btn</button>
    let btn = document.getElementById('btn');
    btn.addEventListener('click', getName); // hello dom
    ```


<br/>

<br/>

## 箭頭函式
會打破以上規則，所有的 `this` 放在箭頭函式中，都會指向函式被宣告時，所在的位置。

```js
// 箭頭函式宣告在 window (global) 時
let name = 'window';
let getName = () => {
    console.log(this.name);
}

getName(); // window
```

<br/>

<br/>

## 小結
綜合以上2點:
1. 非箭頭函式 this 指向 `使用此函式的物件`。
2. 箭頭函式 this 指向 `定義此函式的物件`。