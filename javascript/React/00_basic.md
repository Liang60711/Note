## React 特點
* 組件化
    1. 以往 JS 模塊化，是將 JS 程式拆分成幾個小區塊，可重複使用。
    2. React 組件化是指將 html/css/js 做成可重複使用的`組件(Component)`。

* 採用組件化模式、聲明式編碼，提高開發效率。
* React Native 可使用 Reactm語法進行移動端開發。
* 使用虛擬 DOM + Diffing 算法，盡量減少與真實 DOM 的交互(重要)。

<br/>

<br/>

## 文件說明
1. `babel.min.js`: 可以將jsx語法轉換成js語法提供給瀏覽器讀懂。
2. `react.development.js`: react核心庫。
3. `react-dom.development.js`: react的擴展庫。

<br/>

<br/>

## Hello React
```html
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<div id="test"></div>

<!-- 需注意是babel，不是jsx -->
<script type="text/babel">
    //1.創建虛擬DOM，不用加引號，視為虛擬DOM
    const VDOM = <h1>Hello, React</h1>
    //2.渲染虛擬DOM到頁面
    ReactDOM.render(VDOM, document.getElementById('test'))
</script>
```