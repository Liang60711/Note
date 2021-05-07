# 乙太網路
## Point
* 可以使用 **cisco packet tracer student** 模擬網路架構。
* 參考[蕭老師教學網站](http://www.csie.sju.edu.tw/cm/course/list.htm)

## 乙太網路介紹
* **乙太網路協定**涵蓋的範圍稱為區域網路 (LAN，local area network)。

* 傳輸介質: 匯流排拓樸 (Bus Topology)，可視為資料傳輸的通道。
* 訊號碰撞時，使用 **CSMA/CD** 技術來解決。

<br/>

<br/>


## 乙太網路傳送機制
### 傳輸方式: 
1. **單播 (unicast)** 
2. **廣播 (broadcast)** 發給所有 MAC 地址資料，強迫每台設備接收。
3. **群播 (multcast)**




||單播 Unicast|廣播 Broadcast|群播 Multicast|
|--|--|--|--|
|傳送方式|一對一|一對多|一對指定多|
|傳送內容|廣播區域內都會發送，最後只有一台 符合 MAC 地址的設備接收，其他drop|發給所有 MAC 地址資料，強迫每台設備接收 (廣播區域內設備會跳過MAC檢查)，全部設備都會收到|廣播區域內發送給特定的群組設備，其餘drop|
|MAC位址|網路卡中|FF:FF:FF:FF:FF:FF|前6碼為01:00:5E，後6碼要搭配IP來計算|
|風險||IPv4中，會有廣播病毒的問題||


<br/>

<br/>

## Hub 集線器
* hub 是共享匯流排，會產生碰撞。
* 碰撞 (collision): 兩個資料傳輸設備，在相同的時間點上使用相同的傳輸通道來傳送資料，導致彼此的資料訊號互相干擾而無法辨識發生碰撞的資料傳輸雙方，均必須隨機等待一段時間後，再重送。碰撞時所正在傳送的資料封包碰撞愈多，網路的使用效率也隨之變差。

* 碰撞區域 (collision domain): 無法同時傳送資料的區域。

* 現今已無集線器，交換器的每一個 port 皆為一個碰撞區域，故已無碰撞問題。

## Switch 交換器
* 前身為 **Bridge 橋接器**: 只有兩個 port，兩個獨立匯流排 (兩個碰撞區域)，故不會各自碰撞，port 1、port 2 可以互傳資料不產生碰撞。
* 交換器 即為 多埠的橋接器 (切割為多個碰撞區域)；每兩個 port 之間都有一個獨立的匯流排。
* 傳輸方式: 為 1 對 1 單播傳送；經由交換器內部的記憶體 (RAM) 紀錄的 **MAC table**；廣播預設是關閉的，需使用指令。
* Mac table 學習: 紀錄每台設備傳送資料的 src MAC，並記錄在表上。若 fa0/4 沒有學習到地址，則會用 Hub 的方式傳給所有設備；MAC table 的存活時間為預設 300 秒。
    |Port no.|MAC address|
    |--|--|
    |fa0/1|0006.2AD6.E51B|
    |fa0/2|0001.C970.D556|
    |fa0/3|0060.5C9A.4950|
    |fa0/4||

* MAC Address Table Flooding 攻擊: 將MAC table 的記憶體灌爆，交換器就會回歸 Hub 的傳送方式 (unicast) 稱為 flooding，是一種交換器除錯的方式；。

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

## Router 路由器
### 簡介
* 路由器為分割**廣播區域**的設備，集線器和交換器都無法分割。

* 現今的 **廣播區域** 等於 **網路區域 (Network Segment)**。

* 繞送 (routing): 路由器尋找 <code> routing table </code> 並傳送資料給其他 ip 位址的動作。若沒有查詢到，則會失敗，並 drop 傳輸資料。

<br/>

<br/>

# 子網路切割
目的: 將主機數量太多的**IP區塊** 進行切割，通過**借位 (borrow)**，產生更多的**子網路區塊**。

|IP類別|第一個十進位|子網路遮罩|
|--|:--:|--|
|A|1-127|225.0.0.0|
|B|128-191|225.225.0.0|
|C|192-223|225.225.225.0|

<br/>

舉例: 將 <code>192.168.10.0 / 24</code> 的 1 位主機位元，借位給網路位元，成為 <code>192.168.10.0 / 25</code>；則會產生兩個子網路區塊 <code>192.168.10.0 ~ 127 / 25</code> 及 <code>192.168.10.128 ~ 255 / 24</code>，以此類推。