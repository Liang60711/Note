## 消息中間件 概念
* MQ，消息隊列，儲存消息的中間件。
* 實現中間件的前提: 需要遵守相同的協議(如:AMQP)或規範，才能達成通信。

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