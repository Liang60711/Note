## Java8 記憶體架構圖
> 架構圖 https://www.uj5u.com/houduan/294369.html

> https://www.cnblogs.com/javastack/p/15153856.html

`棧(stack)`:   
1. 大小固定
2. 用於儲存`區域變數`和`基本8種資料型別`
3. 如果是儲存陣列，則會儲存陣列的地址，並指向heap。
4. 調用方法時，會將方法區中`類`的方法，移入棧(stack)中，調用結束後，才會在棧(stack)中釋放。
5. 每個執行緒(thread)各自獨立一個棧區。

`堆(heap)`: 
1. 大小不固定
2. 用於儲存`實體物件`，也就是 new 出來的內容。
3. 用於存儲stack中`陣列`所指向的內容(同2，陣列也算實體物件)。
4. 各個執行緒(thread)都共享一個堆區，為JVM中最大塊的記憶體。
5. Heap 包括各執行緒共用的`字串常數池`、`靜態變數`。
6. 有Garbage Collector機制清除物件。


`方法區`
1. 位在本地記憶體中，非JVM記憶體。
2. 儲存`類`相關的資訊。
3. 與堆區一樣，是被執行緒共享的區域。

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