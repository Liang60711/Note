## UML class diagram
>介紹: https://ithelp.ithome.com.tw/articles/10224271

>箭頭含意 https://terryjryeh.blogspot.com/2019/02/uml-class-diagrams_26.html

UML (Unified Modeling Language，統一建模語言)，用於描述軟體系統的不同方面，包括結構、行為、互動等。UML 提供了多種圖表類型，`Class Diagram 就是其中的一種圖`。

1. class diagram 組成
    * class name
    * attribute
    * function

2. visibility 可視度
    * `+` public
    * `#` protected
    * `-` private
    * `~` package

3. stereotype
    * `<< interface >>`  雙箭頭號代表介面 
    * *`abstract_class`*  斜體代表抽象類別

4. multiplicity 個體數目，(`聚合關係aggregation` || `組成關係composition`) 會用到
    * `0..1` zero to one ()
    * `n` specific number
    * `0..*` zero to many (一個使用者有零張或多張訂單)
    * `1..*` one to many (一間房子有一個或多個廁所)
    * `m..n` specific number range


<br/>

<br/>

## Class Diagram 和 ER Diagram 差異

`Class diagram`:
* 偏向系統層面。
* 每個`類`包含的內容，如: 屬性、屬性類型、方法、方法的返回類型、方法的參數、類的關係（繼承、聚合等）。另外還有關於接口，抽像類。


`ER diagram`:
* 偏向資料庫層面，可以理解為 Table schema 圖像化。
* 包含: 資料表、各實體之間關係、主鍵、外鍵，弱實體，
