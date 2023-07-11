## 多線程的關係
任務會異步的進行處理，處理不管結束沒有，都回返回 Future 物件，有點類似 JS 中 Promise 的概念。

|角色|通常會使用的類|
|--|--|
|任務(Task)|Callable<T>, Runnable|
|處理任務的處理器|Executor, thread pool|
|執行任務後的結果|Future|

<br/>

<br/>

## Future 和 Executor 關係
有一個任務，提交給了Future，Future替我完成這個任務。期間我自己可以去做任何想做的事情(在別的線程作業)。一段時間之後，我就便可以從Future那兒取出結果。就相當於下了一張訂貨單，一段時間後可以拿著提訂單來提貨，這期間可以做別的任何事情。其中Future 接口就是訂貨單，真正處理訂單的是Executor類，它根據Future接口的要求來生產產品。


<br/>

<br/>

## Future 家族
為 Executor 執行後返回的結果(`Execute()`沒有返回結果，`submit()`才有)，以下為Future類的5個定義的方法

```java
// 取消任務
boolean cancel(boolean mayInterruptIfRunning);

// 獲取任務執行結果
V get() throws InterruptedException, ExecutionException;

// 獲取任務執行結果，帶有超時限制
V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException,  TimeoutException;

// 判斷任務是否已經取消
boolean isCancelled();

// 判斷任務是否已經結束
boolean isDone();
```

<br/>

<br/>

## CompletableFuture

> https://popcornylu.gitbooks.io/java_multithread/content/async/cfuture.html

可拆解為 Future 和 completable 來解析。

CompletableFuture是一個 Future 的實作，以下是類中定義的幾個靜態方法。

<br/>

|Method|Description|
|--|--|
|runAsync(Runnable runnable)|非同步的執行一個沒有回傳值的task，並且在預設的thread pool中執行。預設為 ForkJoinPool.commonPool()|
|runAsync(Runnable runnable, Executor executor)|非同步的執行一個沒有回傳值的task，並且在指定的thread pool之中執行。|
|supplyAsync(Supplier supplier)|非同步的執行一個有回傳值的task，並且在預設的thread pool之中執行。|
|supplyAsync(Supplier supplier, Executor executor)|非同步的執行一個有回傳值的task，並且在指定的thread pool之中執行。|

<br/>

<br/>

Completable 可以分成四種特性

* `Completable`: 表示這個 Future 是可以被完成的，用來檢查是否已經正常完成。

    |Method|Description|
    |--|--|
    |complete(T t)|完成非同步執行，並回傳結果|
    |completeExceptionally(Throwable ex)|非同步執行不正常的結束|

    <br/>

* `Listenable`: 類似 callback，可以在執行完成後繼續呼叫函數。

    |Method|Description|
    |--|--|
    |whenComplete()|當完成時，把result或exception帶到callback function中|
    |handle()|當完成時，把result或exception帶到callback function中，並且回傳最後的結果|

    <br/>

    ```java
    CompletableFuture.runAsync(() -> {
        try {
            Thread.sleep(1000);
            System.out.println("hello");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }).whenComplete((result, throwable) -> { // callback
        System.out.println("world");
    });
    ```

    <br/>

* `Composible`: 在異步程式碼下，為了不像JS一樣有callback hell，這邊也是有可以組合異步的寫法。

    |Method|參數Type|return Type|
    |--|--|--|
    |thenRun()|`Runnable`|`CompletableFuture<Void>`|
    |thenAccept()|`Consumer<T>`|`CompletableFuture<Void>`|
    |thenApply()|`Function<T, U>`|`CompletableFuture<U>`|
    |thenCompose()|`Function<T, CompletableFuture<U>>`|`CompletableFuture<U>`|


    ```java
    CompletableFuture
        .runAsync(() -> sleep(1000))
        .thenRunAsync(() -> sleep(1000)) // JS裡面的then()
        .thenRunAsync(() -> sleep(1000))
        .whenComplete((r, ex) -> System.out.println("done"));
    ```

    <br/>

* `Combinable`: 有時候並不會只開出一條分支出去，甚至一條分支出去後還可能岔出更多分支出來，這種類似遞迴的情況常出現在爬蟲等操作上，CompletableFuture 提供對於兩個 CompletableFuture 類的整合處理，針對兩個以上的複數時，則再透過兩兩配對的方式逐一縮合為一。


    |方法名稱|說明|
    |--|--|
    |runAfterBoth (CompletionStage, Runnable runnable)|整合兩個無回傳值的 CompletableFuture|
    |thenAcceptBoth (CompletionStage, Runnable runnable)|整合兩個有回傳值的 CompletableFuture，但不能再回傳結果|
    |thenCombine (CompletionStage, Runnable runnable)|整合兩個有回傳值的 CompletableFuture，須再回傳結果|


<br/>

<br/>

## Executor 家族

> https://hackmd.io/@KaiChen/HyJwzDqxu

* 程式架構

    <img width="80%" src="https://i.imgur.com/uqUSHtx.png">


<br/>

<br/>

## Executor
為一個 interface，只有定義一個方法 `execute()`，不會回傳值。

<br/>

<br/>

## ExecutorService
繼承 Executor 的 interface，因為 Executor 的方法太少，由此 interface 做擴充，有以下幾個方法。

|Methods|Description|
|--|--|
|submit|可以呼叫沒有回傳值的task(Runnable)跟有回傳值的task(Callable)，並且會回傳一個Future|
|invokeAll|一次執行多個task，並且取得所有 task 的future objects|
|invokeAny|一次執行多個task，並且取得第一個完成的 task 的 future object |
|shutdown <br/>shutdownNow|讓整個thread pool 的 threads都停止|
|awaitTermination|等待所有shutdown後的task都執行完成|

<br/>

<br/>

## ScheduledExecutorService
繼承 ExecutorService 的 interface，除了可以做原本 submit task 到 thread pool 以外，還可以讓這個task不會立刻執行或是定時執行task。

<br/>

<br/>

## Thread Pool - Basic Pool
大部分的 thread pool 都是由 `Executors` 工廠類產生，不會自己寫 thread，此類包含了許多 factory 方法。

|Name|Description|
|--|--|
|Executors.newSingleThreadPool()<br/>Executors.newSingleThreadScheduledExecutor()|產生一個只有一個thread的thread pool|
|Executors.newFixedThreadPool(int nThreads)|產生固定thread數量的thread pool|
|Executors.newCachedThreadPool()|產生沒有數量上限的thread pool。Thread的數量會根據使用狀況動態的增加或是減少|
|Executors.newScheduledThreadPool(int corePoolSize)|產生固定數量可以做scheduling的thread pool。|
|Executors.newWorkStealingPool()|產生work stealing的thread pool (本thread沒有task時，會去偷其他thread pool中的task來做)|

<br/>

<br/>

## Thread Pool - ForkJoinPool
1. 當一個任務太大或太複雜，會將此任務拆解成小的任務去給 Thread Pool 處理，這個動作為 Fork；將處理好的小任務合併成結果，這個動作為 Join；目的是拆分任務不浪費閒置的 Thread Pool。

2. Work Stealing: 工作竊取是指當某個線程的任務隊列(Deque)中沒有可執行任務的時候，從其他的線程的任務隊列中竊取任務來執行。

3. Java8 的 `parallelstream()`、`CompletableFuture.runAsync()` 方法也是預設使用 ForkJoinPool

> https://blog.csdn.net/dhaibo1986/article/details/108737347


<br/>

<br/>

## Mono / Flux

[Mono筆記](./Framework/SpringBoot/23_WebFlux_Reactor.md/#mono)

