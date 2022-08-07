## POJO / JavaBean
`POJO` ，Plain Old Java Object，簡單Java物件，有以下幾個原則:
1. 簡單、普通的 Java 物件
2. 可包含業務處理邏輯或資料持久化邏輯
3. 不可繼承或實現他類 (不包含實現序列化)
4. 不受限制的類，包含上述為不可繼承、不可實現、不可作 Annotation 處理 (然而現在越來越多 @Annotation 風格的 POJO 導致這部分已不符合技術潮流而拿掉)。

`JavaBean`，有以下幾個原則: 

1. 需要有一個 public 的無參數建構子
2. 所有屬性設置為 private
3. 屬性需透過 setter/getter 處理，並有命名的規範
4. 可序列化

`兩者最大差別為 JavaBean 不會有業務邏輯處理部分，但 POJO 可以`。

<br/>

<br/>

> https://hackmd.io/@KaiChen/SkCO8r9O_


`POJO` 在不同狀況下又分為下列幾種，以 DTO 和 VO 較常用，SpringBoot 已將其他封裝:

|名稱|全名|使用情境|
|--|--|--|
|PO|Persistant Object|與資料來源直接相關的類|
|`DTO`|Data Transfer Object|與資料傳輸相關的類|
|`VO`|Value Object|資料與使用者直接相關的類|
|DAO|Data Access Object|封裝與 DB 連動，並轉換資料為 PO 的類|
|BO|Buesiness Object|負責集合 PO/VO 相關的類|

<br/>

<br/>

VO 與 DTO 最大的區別在於:

* VO 通常是作為傳與前端展示給客戶、使用者查看的資料類別
* DTO 則是後端負責跟諸多服務溝通用的資料類別


<br/>

<br/>

## EJB, Enterprise JavaBean
* 有以下3種:

    1. Session Bean
    2. Entity Bean
    3. MessageDriven Bean

    <br/>

    <img src="https://upload.wikimedia.org/wikipedia/commons/0/0c/Java_EE_Enterprise_Java_Bean.JPG" width="70%">