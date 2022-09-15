## 同步 Synchronize / 非同步 Asynchronize
1. 同步: 程式依照順序執行(一次只能執行一個動作，與字面意義相反)。
2. 非同步: 程式同時執行。
3. JS為何能執行非同步: 
    * JS為單執行續，理論只能執行同步，但瀏覽器中有許多非同步的 `WebAPI`(ex: document、XMLHttpRequest、setTimeout)，這些API不在V8引擎中，是無法取得的內容，可以進行呼叫來產生非同步的效果。
4. 瀏覽器內，只有 Javascript 引擎本身是同步的，而 Javascript 引擎可以跟 WebAPI 溝通，達到非同步的事件處理。

<br/>

<br/>


## 

<br/>

<br/>

