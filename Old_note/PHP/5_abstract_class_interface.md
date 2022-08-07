# 抽象類別（Abstract Class）
「多個類別（Class）」之間有共同的方法（function）或屬性（attribute）時，可以將這些共用的地方寫成「抽象類別（Abstract Class）」，讓其他的「類別（Class）」去繼承。

1. abstract class 不能直接 new 出來。

2. 只要 class 裡面有 abstract function，就必須被宣告為 abstract class。


<br/>

<br/>

# 介面（Interface）
「多個類別（Class）」之間有共同的方法（function），但方法實做的方式有差異，可以將這些共用「方法」寫成「介面（Interface）」，讓其他的「類別（Class）」去實做這個介面。  

1. 可以被多重 繼承(extends)，子介面會擁有父介面的所有規範。但父介面之間的 function 不能重名。

2. interface 裡只能有public function，且都是空的(不能實作)。
3. 不能直接 new 出來，要先有 class 去implement。

<br/>

<br/>


||類別<br/>(Class)|抽象類別<br/>(Abstract Class)|介面<br/>(Interface)|
|:--:|:--:|:--:|:--:|
|常數(const)|O|O|O|
|屬性(attribute)|O|O|X|
|抽象方法(abstract function)|X|O|O|
|實作方法內容(function())|O|O|X|
|實例化|O|X|X|
|類別是否可繼承多個|X|X|O|