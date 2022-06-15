# 存取權限控制

### 存取權限修飾子
1. private
    * 僅此類別(class)內有效。

2. (default)
    * 只可用在同 class 和 package，即`此套件 package 內有效`。

3. protected
    * 此套件 package 內有效。
    * 不同 package 繼承 class 後，也可以使用。

4. public  
    * 都可以使用。

<br/>

|修飾子|同一類別|同一套件|不同套件的子類別|不同套件且非子類別|
|:--:|:--:|:--:|:--:|:--:|
|public|●|●|●|●|
|protected|●|●|●||
|(default)|●|●|||
|private|●||||

<br/>

<br/>

<br/>

<br/>

### fianl
final 即最後，修飾後即不可繼承或更改值
1. class  
    * final class 的類別不能被 extends。

2. method
    * 不能被覆寫(override)。

3. variable
    * 在初始化後不能更改。

4. 屬性
    * 即常數，`必須賦值或在建構子中初始化賦值`。

        ```java
        // 通常會加上static
        static final int NUM = 10;
        ```

<hr>

### static
系統一開始就產生並佔據記憶體。


<hr>

### new 
產生實體物件，其變數也佔據記憶體。

<hr>

### override, overload 差異

* override： 指一個子類別將會 extends 父類別，並改寫父類別已定義好的方法。
方法名稱、參數型別、回傳型別需要相同，`回傳的值可以不同`；`子類權限修飾符需大於等於父類`，例如父類方法是proteced，子類必須是protected或public。

* overload： 同一個 class 裡用同一個方法名，但參數不同(參數數量 或 參數型別)。用途: 藉由不同的參數，去調用不同的函式。


<br/>

<br/>

## assert
檢查錯誤，有以下兩種:
1. `assert <boolean表示式>`

    如果<boolean表示式>為true，則程式繼續執行。

    如果為false，則程式丟擲AssertionError，並終止執行。

 

2. `assert <boolean表示式> : <錯誤資訊表示式>`

    如果<boolean表示式>為true，則程式繼續執行。

    如果為false，則程式丟擲java.lang.AssertionError，並輸入<錯誤資訊表示式>。

<br/>

IntelliJ編輯器需要在 `add VM options` 加入參數，才能使用。
>步驟: https://se-education.org/guides/tutorials/intellijUsefulSettings.html

<br/>

<br/>