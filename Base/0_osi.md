# OSI 模型 Open System Interface
### PDU: 協議數據單元 Protocol Data Unit  

|Layer|PDU|
|--|--|
|Physical Layer|Bit|
|Data Link Layer|Frame(幀)|
|Network Layer|Packet(包)|
|Transport Layer|Segment(段)|  

<br>

### 1. 實體層 Physical Layer
在線材中傳遞0和1訊號，如網卡、網路線、Hub

### 2. 資料鏈結層 Data Link Layer
協定:乙太網路協定、ARP

將實體層的訊號封裝成一組符合邏輯的傳輸資料，PDU單位為 frame，其中一個 frame 包含 Head 和 Data。Head 中包含 MAC(Media Access Control) 位址。
* Head: 發送者、接收者、數據類型，長度18字節。
* Data: 長度46~1500字節。
* MAC: 為12個16進制數(6廠商號+6流水號)，每台設備的MAC為獨一無二的，可讓自己、對方主機辨識資料來源。MAC地址只與廠商有關，無子網路地址，所以需要在網路層(Network Layer)實現子網路地址。
* broadcast: 廣播，為乙太網路傳送資料的方式，需要在同一個子網路中才可以傳遞。若MAC地址在同一個子網路，使用廣播，若不同子網路，則用路由(route)。  

### 3. 網路層 Network Layer
協定: 通訊協定(Internet Protocol，IP)

網路層定義網路路由及地址功能，讓資料能夠在網路間傳遞。這一層中最主要的通訊協定是網際網路協定（Internet Protocol，IP），資料在傳輸時，該協定將IP位址加入傳輸資料內，並把資料組成封包（Packet）。
* IP地址是確定電腦所在的子網路。
* MAC地址是將封包(Frame)發送到子網路的目標網卡上。
* IP地址和MAC地址無直接關係，IP地址是管理員分配，MAC地址是網卡根據網卡上的。
---
IP地址:由32個2進制組成，但習慣以4個10進制表示(範圍 0~255)，如192.168.1.1，前a個數字代表**網路地址**，後b個數字代表**主機**。依照 a, b 分為以下幾類IP位址:  



|分類|字首碼(32碼中的前8碼)|開始位址	|結束位址|子網路遮罩(subnet mask)|
|--|--|--|--|--|
|A類|第1位為0|0.0.0.0|127.255.255.255|255.0.0.0|
|B類|第1、2位為10|128.0.0.0|191.255.255.255|255.255.0.0|
|C類|第1、2、3位為110|192.0.0.0|223.255.255.255|255.255.255.0|
|D類(群播)|第1、2、3、4位始終為1110|224.0.0.0|239.255.255.255|未定義|
|E類(保留)|第1個8位中的第1、2、3、4位始終為1111|240.0.0.0|255.255.255.255|未定義|
<br> 

**子網路遮罩**(subnet mask): 為4組0或255數字，用來區分何者為**網路地址**，何者為**主機**，若為255(即2進制11111111)，則為網路地址；0(即2進制00000000)，則為主機。  

---
IP封包(Packet): 分為 Head、Data，Head包含版本、長度、IP地址；Data包含數據內容，最大長度(Head+Data)為65535字節。因為乙太網路封包(frame)的Data最多傳遞1500個字節，故Packet過長時，需要切割成多個Packet，再包裹成 Frame。  

端口(Port): 範圍為0~65535(2^16-1)，0~1023為系統使用，用來區分一台電腦上不同程序所使用號碼，以確保封包(Packet)傳遞到對的程序中。  
Head: 長度20~60字節  

### 4. 傳輸層 Transport Layer
協定: 傳輸控制協定（Transmission Control Protocol，TCP）、使用者資料報協定（User Datagram Protocol，UDP)

相較網路層(Network Layer)，是主機對主機的傳遞，傳輸層(Transport Layer)則是端口(Port)對端口的傳遞。  

---
Socket: 為API的一種，網路上的兩個程序通過一個雙向的通信連線實現資料的交換，這個連線的一端稱為一個socket(IP 地址 + Port)。  

TCP: 分為Head和Data，每個封包分配一個唯一的識別碼和一個序號，當接收端收到封包後，如果順序正確，會向發送端傳送一個確認信號(Acknowledgement)，以此確認接收端已經收到封包，並發送端傳送另一個封包。如果封包遺失或發送順序錯誤，接收端會保持沈默，不發送確認信號。這表示發送端需要重新傳送封包。

UDP: 同樣分為Head和Data，Head包含發出端口(Port)和接收端口，總長度為65535字節。發出數據不會確認，所有有可能造成封包丟失。  

||TCP|UDP|
|--|:--:|:--:|
|可靠性|可靠|不可靠|
|速度|慢|快|
|傳輸方式|封包按順序傳輸|封包以串流方式傳輸|
|錯誤檢查與修正|有|無|
|壅塞控制|有|無|
|確認|有|只有檢查碼|
|適用服務|要求可靠傳輸的服務，例如電子郵件、網頁瀏覽、檔案傳輸|即時服務，例如串流媒體、網路電話、網路遊戲|
|Protocol Data Unit，PDU|Segment|Datagram|  


<br>

### 5. 應用層 Application Layer
協定: 動態主機設定協定(Dynamic Host Configuration Protocol，DHCP)、網域名稱系統(Domain Name System，DNS)

DHCP: 建立在UDP(User Datagram Protocol)之上，由於靜態IP不夠靈活，所以使用動態IP，所以有了DHCP，用於使用動態IP時，會被電信公司分配到一個IP地址。  

DNS: 將網域名稱(www.amazon.com)轉譯為IP地址(192.0.2.44)。

<br>

若電腦1要傳給電腦2，則有以下兩個情況:
|情況|封包地址|
|--|--|
|同一個子網路|對方的MAC地址、對方IP地址|
|不同一個子網路|對方Gateway的MAC地址、對方的IP地址|  

<br>

### 連線到 google 步驟
1. DNS 服務器將 www.google.com 轉譯成 IP地址(使用UDP)。
2. 本地電腦需判斷是否此IP是否同在一個子網路，此時需要**子網路遮罩**。
3. 若為不同的子網路，則須經過一個 gateway網關(route路由)，接收方的MAC地址是gateway網關的MAC地址，並需要接收方IP地址。
4. 瀏覽網頁使用 HTTP 協定(使用TCP，非UDP)，若 HTTP 封包太長，則會分成好幾個封包依序發送。