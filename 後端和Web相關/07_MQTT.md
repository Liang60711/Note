## MQTT(Message Queuing Telemetry Transport) 訊息序列遙測傳輸


> 來源1: https://ithelp.ithome.com.tw/articles/10224407

> 來源2: https://swf.com.tw/?p=1002

1. 為一種輕量級協議，相對於HTTP省略許多 header 資料，所以需要的網路頻寬與硬體資源是相對低的。

2. MQTT有四個主要的元素(發布者和訂閱者可以是同一個)
   
   * 發布者(Publisher)
   
   * 訂閱者(Subscriber)
   
   * 主題(Topic)
   
   * 轉訊站(Broker)

<br/>

<br/>

## 發布流程

1. 發布者(Publisher) 向 轉訊站(Broker) 發布一個 主題(Topic)。

2. Broker 而後專發給所有 Subscriber。

3. 目前的 Subscriber和Broker之間可以進行雙向溝通。

<br/>

<br/>

## 封包格式
一個 MQTT 封包，由3種部分組成: `Header`、`topic`、`payload`。

不同於HTTP的標頭採用文字描述，MQTT的標頭採用數字編碼，整個長度只佔2位元組，等同兩個字元，後面跟著訊息的主題（topic）和內容（payload）


<img src="https://swf.com.tw/images/books/IoT/MQTT/mqtt_message_format.png">

> 圖片來源: https://swf.com.tw/?p=1002

<br/>

<br/>

## Qos級別

定義了傳輸品質，有分三個級別

1. `Qos 0` :  
   
   * 最多傳送一次，不保證送達，Publisher 傳訊給 Broker 後直接轉傳給 Subscriber，不會回傳確認封包
   
   * 缺點是資料可能會遺失

2. `Qos 1` : 
   
   * 至少傳送一次，Publisher 傳訊給 Broker 後，Broker會回應 PUBACK 訊息給Publisher，確認收到訊息，Publisher沒有收到PUBACK回應，就會再次發送Publish。
   
   * 缺點是若沒收到 PUBACK，可能會讓 Publisher 收到重複訊息。

3. `Qos 2` :
   
   * Publisher 傳訊給 Broker 後，Broker會回應訊息給 Publisher，確認收到要發布的訊息，Publisher 收到回應時，傳送PUBREL (釋放發布訊息)，Broker收到PUBREL，將訊息發布給 Subscriber，並向Publisher回報。
   
   * 缺點是佔用頻寬與傳送時間較多


<br/>

<br/>

## MQTT配置

> https://blog.csdn.net/beibei3321/article/details/124187758