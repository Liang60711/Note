## UML class diagram
>介紹: https://ithelp.ithome.com.tw/articles/10224271

>箭頭含意 https://terryjryeh.blogspot.com/2019/02/uml-class-diagrams_26.html


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