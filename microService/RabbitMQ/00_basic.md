## 消息中間件 概念
* MQ，消息隊列，儲存消息的中間件。
* 實現中間件的前提: 需要遵守相同的協議(如:AMQP)或規範，才能達成通信。

* 優點: 

    1. 將 Producer 和 Consumer 解耦。
    2. Producer 和 Consumer 不需要知道雙方的實際的位置(如：IP address)，只要將資料往 MQ 送就好。
    3. 即使 Consumer 短暫的無法提供服務也沒關係，MQ 可以將資料暫存起來，等待 Consumer 重新上線時再送過去。
    4. 比起持續 Polling 的方式相對有效率的多。
    5. 提供了一個可靠的方式，讓訊息傳遞 & 工作處理兩件事情可以用非同步的方式進行。
    6. 當單一 Consumer 不足以完成所有工作時，可以很容易的增加 Consumer 數量進行水平擴展。

<br/>

<br/>

## 角色
1. 生產者 Producer

    * 負責丟訊息到 Queue 中
    * 若有定義 Exchange，則丟給 Exchange 決定要給誰
    
2. Exchange 
    
    * 用來決定 Producer 給的資料要丟給哪一個 Queue
    * 主要有這四種方式

        `direct`: 直接丟給指定的 Queue

        `topic`: 類似 regular expression，設定 binding 規則，丟給符合的 Queue

        `headers`: 透過傳送資料的 header 來特別指定所要的 Queue

        `fanout`: 一次丟給全部負責的 Queue

    * 與 Queue 的關係為一對多

3. 隊列 Queue

    * 負責存放所需要的資料
    * 跟資料結構的 Queue 一樣，有先進先出 (FIFO) 特性
    * 每個 Queue 都會有他的名字當 id

4. 消費者 Consumer

    * 負責接收來自 Queue 的訊息


<br/>

<br/>

## Reference

> 安裝 https://kucw.github.io/blog/2020/11/rabbitmq/

> 各種參數 https://godleon.github.io/blog/ChatOps/message-queue-concepts/