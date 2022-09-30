## 同步 Synchronize / 非同步 Asynchronize
1. 同步: 程式依照順序執行(一次只能執行一個動作，與字面意義相反)。
2. 非同步: 程式同時執行。
3. JS為何能執行非同步: 
    * JS為單執行續，理論只能執行同步，但瀏覽器中有許多非同步的 `WebAPI`(ex: document、XMLHttpRequest、setTimeout)，這些API不在V8引擎中，是無法取得的內容，可以進行呼叫來產生非同步的效果。
4. 瀏覽器內，只有 Javascript 引擎本身是同步的，而 Javascript 引擎可以跟 WebAPI 溝通 (WebAPI將非同步事件丟進`事件佇列`)，達到非同步的事件處理。

<br/>

<br/>


## 事件佇列 Event Queue
> 參考詳細流程: https://medium.com/itsems-frontend/javascript-event-loop-event-queue-call-stack-74a02fed5625
1. 可實現 javascript 中非同步的方法。
2. 若程式執行到非同步時，會先將此段程式放進至`事件佇列`中，等 `call stack` 中的同步程式都執行完成了，再將此非同步丟進 `call stack` 中執行。


<br/>

<br/>

## await 用法
> https://ithelp.ithome.com.tw/articles/10205116


`await` 在收到 Promise 回應前，會暫停執行後續的程式。
```javascript
// then寫法
getData()
    .then(res => console.log(res));

// await寫法 (getData()還沒回傳前，不會執行下一行)
function getDataAwait() {
    var res = await getData();
    console.log(res);
}
```

<br/>

<br/>

## jquery async: false 選項
> https://stackoverflow.com/questions/1478295/what-does-async-false-do-in-jquery-ajax

1. 預設是 async: true
2. 改為 false 代表程式會暫停，收到回傳後再繼續。
