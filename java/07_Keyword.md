## volatile 

> https://cloud.tencent.com/developer/article/1803803

此關鍵字用來宣告成員變數，在多線程開發時，當共享變數被不同的 thread 處理時，會遇到此問題 : 變數被 thread1 更新時，thread2 也需要知道最新更新的變數，否則會造成最終數據不一致。

以下是 volatile 關鍵字的主要用途：

1. `可見性（Visibility）` ： 當一個變量被聲明為 volatile 時，對該變量的讀取和寫入操作具有可見性。這意味著當一個線程修改了這個變量的值時，其他線程可以立即看到這個修改。這可以用於確保多個線程之間對於共享變量的操作都能及時感知到最新的值。

2. `禁止指令重排序（Prevents Instruction Reordering）` ： 代碼在執行的過程中的先後順序，Java 在編譯器以及運行期間的優化，代碼的執行順序未必就是編寫代碼時候的順序。 volatile 關鍵字可以禁止指令進行重排序優化。

3. `適用於單讀多寫的情況` ： 當變量被多個線程寫入，但只有一個線程進行讀取操作時，可以將該變量聲明為 volatile。這樣可以避免使用更重量級的同步機制（如 synchronized 或 Lock），提高性能。


需要注意的是，volatile 關鍵字不能保證`原子性（Atomicity）`。

* 原子性的討論是存在於多線程的情況。

* 原子性是指對變量的讀取和寫入操作是不可分割的，要麼全部完成，要麼都不完成。如果需要保證原子性，可以使用 synchronized 關鍵字或使用原子類（Atomic Classes）來實現。

    * 全部完成是指此三個動作不可被分割 : 讀取變數 -> 做變數計算count++ -> 回存變數

* 例如 `int count = 0`，當 thread A 和 thread B 同時要做 `count++`，會產生 race condition，原本預期答案是 2，但有可能會覆蓋，產生 count 為 1。

<br/>

volatile 和 synchronized 比較: 

1. 可見性和有序性

    * volatile: 可確保`可見性`、`有序性`。
    * synchronized: 可確保`可見性`、`有序性`、`原子性`。

2. 使用場景

    * volatile: 適用於一個線程修改變量的值，並讓其他線程能夠立即看到最新值的場景，例如標記共享標誌、開關、計數器等。
    * synchronized: 適用於需要確保多個操作的原子性，並保證同一時間只有一個線程能夠訪問共享資源的場景，例如多個線程操作共享數據結構或共享變量。

3. 舉例

    * `庫存數量`的計算，應該使用 synchronized，雖然使用 volatile，可以讓不同線程看到最新的庫存數量，但要改動此變數，就必須使用 synchronized 確保此變數不會產生 race condition。

<br/>

<br/>

## 存取權限修飾子
1. private
    * 僅此類別(class)內有效。

2. (default)
    * 只可用在同 class 和 package，即`此 package 內有效`。

3. protected
    * 此 package 內有效。
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

## fianl
final 即最後，修飾後即不可繼承或被重新賦值
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

5. 注意: List 被定義了 final，還是可以 add() 其他元素。

    ```java
    final List<String> list = new ArrayList<>();
    list.add("test");

    System.out.print(list); // ["test"]
    ```

    代表說 final 只是限制 List 不能被重新賦值(記憶體位址改變)，不代表不能改變本身的值，所以 List 的任何方法都是被允許調用的。

    ```java
    // 呈上，若要改變位址指向一個新的物件時，這樣就會報錯
    list = new ArrayList<>();
    ```


<hr>

## static
系統一開始就產生並佔據記憶體。


<hr>

### new 
產生實體物件，其變數也佔據記憶體。

<hr>

## override, overload 差異

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


## 參數 (parameters) vs 引數 (arguments)
* 參數是在函式定義中所列出的變數
* 引數是呼叫函式時傳遞給它的值

    ```java
    // s, i 為參數
    void solution(String s, Integer i){

    }

    // "Apple", 5 為引數
    solution("Apple", 5);
    ```