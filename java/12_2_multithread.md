## 多線程的關係
任務會異步的進行處理，處理不管結束沒有，都回返回 Future 物件，有點類似 JS 中 Promise 的概念。

|角色|通常會使用的類|
|--|--|
|任務(Task)|Callable<T>, Runnable|
|處理任務的處理器|Executor|
|執行任務後的結果|Future|

<br/>

<br/>

## Future 和 Executor 關係
有一個任務，提交給了Future，Future替我完成這個任務。期間我自己可以去做任何想做的事情(在別的線程作業)。一段時間之後，我就便可以從Future那兒取出結果。就相當於下了一張訂貨單，一段時間後可以拿著提訂單來提貨，這期間可以做別的任何事情。其中Future 接口就是訂貨單，真正處理訂單的是Executor類，它根據Future接口的要求來生產產品。


<br/>

<br/>

## Future 家族



<br/>

<br/>

## Executor 家族

> https://hackmd.io/@KaiChen/HyJwzDqxu

* 程式架構

    <img width="80%" src="https://i.imgur.com/uqUSHtx.png">


<br/>

<br/>

## Executor
為一個 interface，只有定義一個方法 `execute()`。

<br/>

<br/>

## ExecutorService
