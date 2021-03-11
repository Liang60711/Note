# 存取權限控制

### 存取權限修飾子
1. private
    * 只可用在同 class。

2. default
    * 只可用在同 class 和 package。

3. protected
    * 只可用在同 class 和 package
* 不同 package 繼承 class 後，也可以使用；等於是介於 private 和 public 之間，增加便利性。

4. public  
    * 都可以使用。

<hr>

### fianl
final 即最後，修飾後即不可繼承或更改值
1. class  
    * final class 的類別不能被 extends。

2. method
    * 不能被覆寫(override)。

3. variable
    * 在初始化後不能更改。

<hr>

### static
系統一開始就產生並佔據記憶體。


<hr>

### new 
產生實體物件，其變數也佔據記憶體。

<hr>

### override, overload 差異

* override： 指一個子類別將會 extends 父類別，並改寫父類別已定義好的方法。方法名稱、參數型別、回傳型別需要相同，回傳的值可以不同。

* overload： 同一個 class 裡用同一個方法名，但參數不同(參數數量 或 參數型別)。用途: 藉由不同的參數，去調用不同的函式。

