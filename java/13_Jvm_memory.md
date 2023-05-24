## Java8 記憶體架構圖

> 詳解 https://blog.csdn.net/CoderBruis/article/details/85240273

<img src="https://img-blog.csdnimg.cn/20190706150944640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQnJ1aXM=,size_16,color_FFFFFF,t_70">

<img src="https://img.uj5u.com/2021/08/18/257437180620071.png">

`Java堆(heap)`和`方法區(Method Area)`是所有線程共享的記憶體區塊。其他三個區塊為線程隔離的數據區。

`Java堆(heap)`: 
1. 大小不固定
2. 各個執行緒(thread)都共享一個堆區，為JVM中最大塊的記憶體。
3. 用於儲存`實體物件`，也就是 new 出來的內容，幾乎所有物件都是在此區分配記憶體的，但 `java.lang.Object` 除外，該物件是在方法區中分配記憶體。
4. 用於存儲stack中`陣列`所指向的內容(同2，陣列也算物件)。
5. 包括各執行緒共用的`字串常數池(String Table)`、`靜態變量`(JDK8)，詳見圖解 https://zhuanlan.zhihu.com/p/166190558。
6. 有Garbage Collector機制清除物件。
7. 此區可拓展，也可固定，一般都是可拓展(透過 -Xmx 和 -Xms 控制)。
8. 若沒有記憶體分配給物件，並且無法再拓展 heap 了，就會拋出 OOM。

`方法區(Method Area)`
1. 位在本地記憶體中，非JVM記憶體。
2. 儲存`類`相關的資訊。
3. 與堆區一樣，是被執行緒共享的區域。
4. 此區包含 `運行時常數池(Runtime Constant Pool)`
5. JDK早期的 `字串常數池(String Table)`、`靜態變量`，在 JDK8已經移到 heap。

<hr/>

`虛擬機棧(VM stack)`:   
1. 大小固定。
2. 用於儲存`區域變數`和`基本8種資料型別`。
3. 如果是儲存陣列，則會儲存陣列的地址，並指向heap。
4. 調用方法時，會將方法區中`類`的方法，移入棧(stack)中，調用結束後，才會在棧(stack)中釋放。
5. 每個執行緒(thread)各自獨立一個棧區。
6. 當線程請求的棧深度大於虛擬機所允許的棧深度時，就會拋出 `StackOverflowError`。
7. 因為虛擬機棧是動態可擴展的，所以當虛擬機棧無法申請到足夠的內存時，會拋出 `OutOfMemoryError`。

`本地方法棧(Native Method Stack)`
1. 用於執行本地方法(Native Method)的一個區域。本地方法是使用非Java語言(如C、C++等)實現的方法，並且在Java程式中通過JNI（Java Native Interface）進行調用。
2. 本地方法棧也會拋出StackOverflowErrot和OOM異常。

`程序計數器(Program Counter Register)`
1. 一塊較小的記憶體區塊。
2. 每個線程都有自己獨立的程序計數器。
3. 功能是確保線程能夠恢復到正確的執行位置，當一個線程被創建時，程序計數器被初始化為0。當線程開始執行時，程序計數器會按照字節碼指令的順序進行增加，指向下一個要執行的指令。
4. 程序計數器在任何時間只會存儲一個值，並且不會發生OutOfMemoryError。


<br/>

<br/>

## 建立物件時，記憶體分析
1. 有一個類，如下。

    ```java
    class Horse{
        String name;
        Int age;

        public void Hello(){
            System.out.println("Hello");
        }
    }
    ```

2. 當建立新物件時:
    ```java
    Horse h1 = new Horse();
    Horse h2 = null;
    ```

    stack會建立物件地址，此地址會指向 heap 中的某區域，此 heap 中會儲存物件屬性。

    <br/>
    
    |棧stack|堆heap|
    |--|--|
    |h1 (產生地址 0x0011)|name=null<br/>age=0|
    |h2 (null不產生地址)||
    
    <br/>

3. 物件之間賦值

    ```java
    Horse h1 = new Horse();
    Horse h2 = h1;// 相同類才能賦值
    ```

    h2也會產生與h1相同地址，並指向heap的同一區域。

    <br/>
    
    |棧stack|堆heap|
    |--|--|
    |h1 (產生地址 0x0011)|name=null<br/>age=0|
    |h2 (相同地址 0x0011)||

    <br/>

4. 垃圾回收
    - 若堆(heap)中沒有被棧(stack)所指向，該heap中的內容就會被標記為垃圾。
    - 因此在確定不使用該物件時，可盡早釋放此物件，`obj = null;`。

<br/>

<br/>

## Reference 

> https://www.uj5u.com/houduan/294369.html

> https://www.cnblogs.com/javastack/p/15153856.html

> https://zhuanlan.zhihu.com/p/166190558