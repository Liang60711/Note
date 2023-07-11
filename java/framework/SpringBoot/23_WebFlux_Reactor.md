## 反應式編程 (Reactive Programming, RX)

> 解釋 https://cloud.tencent.com/developer/article/1602301

重點: 

1. 響應流必須是非阻塞式的。 
2. 響應流必須是一個數據流。 
3. 必須可以異步執行。 
4. 要能夠處理背壓。
5. 當沒有訂閱者時，發布者什麼也不做(因為沒意義)。

舉例，以下是命令式編程:
```java
// 當 a, b值改為 6, 7時，sum不會跟著變動
int a, b, sum;

a = 3;
b = 4;
sum = a + b;

a = 6;
b = 7;

System.out.println(sum);
```


<br/>

<br/>

## WebFlux
1. 為一個與傳統 SpringMVC 相對的一個 Web 框架

    <img src="https://img.jbzj.com/file_images/article/202202/202202240959594.jpg" width=80%>

2. 底層容器使用 Netty，有別於傳統容器(tomcat, jetty, undertow)一個請求對應一個thread，故高併發情況占滿所有 thread 時，會有阻塞問題，或是當請求正在 handler 處理時，讓 thread 等待等同浪費 thread 資源。

    傳統SpringMVC
    
    <img src="https://img.jbzj.com/file_images/article/202202/202202241000005.jpg" width=80%>

    WebFlux

    <img src="https://img.jbzj.com/file_images/article/202202/202202241000006.jpg" width=80%>
    
    Netty 中的一個 thread 可以處理多個請求，Netty 是 `事件驅動`的處理方式，容器中會有專門處理請求的 thread，當請求來時，只需要把請求放到 queue 中，就可以釋放該 thread，而 `worker thread` 會去處理 queue 中的請求，這樣不管處理請求的 thread 還是 worker thread 都不會閒置。 

    <img src="https://ithelp.ithome.com.tw/upload/images/20211004/20141418kVTWP7GQMW.jpg" width=80%>


3. WebFlux 也可以執行在 Servlet3.1+ 容器上(tomcat, jetty, undertow)，但默認是執行在 Netty 容器上。

4. 如果專案使用Spring MVC沒有問題，就沒必要改變，畢竟命令式的開發方式還是最直覺且相對單純，而且大部分的libraries都是阻斷式(blocking)的，如果沒有遇到無法處理的瓶頸就沒必要改變。

<br/>

<br/>


## 加入依賴
```xml
<!-- 整包WebFlux -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>

<!-- Reactor-core -->
<dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-core</artifactId>
</dependency>
```

<br/>

<br/>

## Mono
1. 是Reactor中的一種發布者，表示 0 或 1 個元素的異步序列。 
2. 可以用來處理單一的結果或響應。 
3. 通過使用操作符和方法鏈，可以對 Mono 進行轉換、過濾、映射等操作。 
4. 可以通過訂閱 Mono 來觸發異步操作並處理結果。



<br/>

<br/>

## Flux
1. 是Reactor中的一種發布者，表示 0 到 N 個元素的異步序列。
2. 可以用來處理多個元素、多個事件源或數據流的情況。
3. 可以對 Flux 進行轉換、過濾、映射等操作，以便對元素進行處理。
4. 可以通過訂閱 Flux 來觸發異步操作並處理元素流。

<br/>

<br/>

## 背壓（Backpressure）
1. 是一種用於處理異步流中的 `生產者-消費者` 之間速度不匹配情況的策略。當生產者產生數據的速度大於消費者處理數據的速度時，就會產生背壓。
2. 背壓機制可以通過控制生產者的生產速度，使其適應消費者的處理速度，以避免內存溢出或資源耗盡等問題。
3. 在 Reactor Core 中，常見的背壓策略包括:

    * BUFFER：生產者產生的數據會被緩存，直到消費者處理完畢；缺點是緩存太多系統也還是會崩潰。 
    * DROP：當生產者產生數據的速度超過消費者處理數據的速度時，新產生的數據會被丟棄；雖然系統不會崩潰，但會失去生產者發布的資料。 
    * LATEST：當生產者產生數據的速度超過消費者處理數據的速度時，只保留最新產生的數據，丟棄之前的數據。

4. 範例

    ```java
    // Buffer
    Flux.range(0,100)
        .onBackpressureBuffer()
        .subscribe(System.out::println);
    
    // Drop
    Flux.range(0,100)
        .onBackpressureDrop()
        .subscribe(System.out::println);
    
    // Latest
    Flux.range(0,100)
        .onBackpressureLatest()
        .subscribe(System.out::println);
    ```


<br/>

<br/>

## API 使用

> https://blog.csdn.net/weixin_47951400/article/details/124292452

just()，從已知內容和大小的數據創建
```java
Flux.just(new Integer[]{1,2,3})
    .subscribe(System.out::println);//沒有訂閱，前面就不會執行

Mono.just(Arrays.asList(1,2,3))
    .subscribe(System.out::println);
```

fromIterable()，從可迭代的物件中創建
```java
// fromIterable() 參數類型為 List<Integer>，回傳類型為 Flux<Integer>
Flux.fromIterable(Arrays.asList(1,2,3))
    .subscribe(System.out::println); 
```

fromStream()，從數據流中創建
```java
Flux.fromStream(Stream.of(1,2,3))
    .subscribe(System.out::println);
```

range()，創建一個範圍內可迭代的物件
```java
Flux.range(0,5)
    .subscribe(System.out::println); // 印出 0,1,2,3,4
```

delayElements()，每個Flux中的元素，每隔x毫秒發送
```java
Flux.fromIterable(Arrays.asList(1,2,3,4))
    .delayElements(Duration.ofMillis(1000L))
    .subscribe(System.out::println);
```

<br/>

<br/>

## Reference 

> WebFlux介紹1 https://www.jb51.net/article/238656.htm

> WebFlux介紹2 https://ithelp.ithome.com.tw/articles/10277436

> 程式範例 https://blog.csdn.net/weixin_47951400/article/details/124292452