# 1. 網路元件介紹
## Point
* 可以使用 **cisco packet tracer student** 模擬網路架構。
* 參考[蕭老師教學網站](http://www.csie.sju.edu.tw/cm/course/list.htm)

## 乙太網路
* **乙太網路協定**涵蓋的範圍稱為區域網路 (LAN，local )。
* 傳輸方式: 以 **廣播 (broadcast)** 方式進行傳輸。
* 傳輸介質: 匯流排拓樸 (Bus Topology)。

<br/>

<br/>


## 網路設備

### Hub 集線器
* hub 是共享匯流排，會產生碰撞。
* 碰撞 (collision): 兩個資料傳輸設備，在相同的時間點上使用相同的傳輸通道來傳送資料，導致彼此的資料訊號互相干擾而無法辨識發生碰撞的資料傳輸雙方，均必須隨機等待一段時間後，再重送。碰撞時所正在傳送的資料封包碰撞愈多，網路的使用效率也隨之變差。

* 碰撞區域 (collision domain): 無法同時傳送資料的區域。

### Switch 交換器
* 前身為 **Bridge 橋接器**: 只有兩個 port，兩個獨立匯流排 (兩個碰撞區域)，故不會各自碰撞，port 1、port 2 可以互傳資料不產生碰撞。
* 交換器 即為 多埠的橋接器 (切割為多個碰撞區域)；每兩個 port 之間都有一個獨立的匯流排。
* 傳輸方式: 為 1 對 1 傳送；經由交換器內部的記憶體 (RAM) 紀錄的 **MAC table**。
* Mac table 學習: 紀錄每台設備傳送資料的 src MAC，並記錄在表上。若 fa0/4 沒有學習到地址，則會用 Hub 的方式傳給所有設備。
    |Port no.|MAC address|
    |--|--|
    |fa0/1|0006.2AD6.E51B|
    |fa0/2|0001.C970.D556|
    |fa0/3|0060.5C9A.4950|
    |fa0/4||

* MAC table 攻擊: 將MAC table 的記憶體灌爆，交換器就會回歸 Hub 傳送，即可拿到資料。

* 查詢與清除 MAC table 指令
    ```
    // 進入管理者模式
    >en

    // 查詢 MAC table 內容
    >show mac-address-table

    // 清空 MAC table 內容
    >clear mac-address-table 
    ```

* switch 設備本身，每個 port 都有獨立的一個 Mac address；舉例若 switch 串接，switch1 和 switch2 會將對方的 Mac address 也記錄在 table 中。

* switch 傳輸方式: 

    1. Store and Forward: 接收到後，確認無誤再轉發，速度慢。
    2. Cut Through: 好壞都照樣傳輸，不具偵錯功能，但速度快。
    3. Fragment-Free: 只偵測前 64bytes (通常 Collision 只會出現在前 64bytes)，小於 64bytes 則會丟棄。

<br/>

<br/>

## 廣播 BroadCast
### 廣播傳送機制

||單點傳送 Unicast|廣播傳送 Broadcast|群播傳送 Multicast|
|--|--|--|--|
|傳送方式|一對一|一對多|一對指定多|
|傳送內容|廣播區域內都會發送，最後只有一台接收，其他drop|廣播區域內設備會跳過MAC檢查，因此全都會接收|廣播區域內發送給特定的群組設備，其餘drop|
|風險||IPv4中，會有廣播病毒的問題||
|MAC位址|網路卡中|FF:FF:FF:FF:FF:FF|前6碼為01:00:5E，後6碼要搭配IP來計算|