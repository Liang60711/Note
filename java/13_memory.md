## 記憶體
> 架構圖 https://www.it145.com/9/180799.html

`棧(stack)`:   
1. 用於儲存`區域變數`和`基本8種資料型別`
2. 如果是儲存陣列，則會儲存陣列的地址，並指向heap。
3. 大小固定

`堆(heap)`: 
1. 大小不固定
2. 用於儲存`物件-屬性`，也就是 new 出來的內容。
3. 用於存儲stack中`陣列`所指向的內容。


`方法區`
1. 用於儲存`物件方法`，但使用方法時，會將方法移入棧(stack)中，調用結束後，才會在棧(stack)中釋放。

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
