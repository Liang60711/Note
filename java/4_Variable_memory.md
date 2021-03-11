# 變數種類

## 區域變數（Local variables）
* 區域變數通常宣告在 方法（method）、建構子（constructor）或 區塊（block）之中。
* 區域變數無法在所屬的 方法（method）、建構子（constructor）或 區塊（block）外使用。
* 區域變數通常在進入 方法（method）、建構子（constructor）或 區塊（block）時建立，並在離開時自動銷毀。
* 無法在區域變數上使用修飾子。
* 區域變數必須有預設值。

## 實例變數（Instance variables）：
* 實例變數會被宣告在類別（class）之中，但在 方法（method）、建構子（constructor）或 區塊（block）外。
* 當物件被使用 new 的方式建立，對應的實例變數也會被建立（匿名物件除外），當物件被清除，變數也一同被清除。
* 實例變數可以使用修飾子。
* 實例變數對所有 方法（method）、建構子（constructor）或 區塊（block）而言都是可見的，建議使用修飾子來調整權限。
* 有預設值, 如果沒有給訂初值, 編譯器也會給予預設值（數字型態為0、字元型態為NULL、布林型態為false。
* 若在同一類別中，實例變數可以直接呼叫其名稱存取，但若在靜態方法或其他類別之中則須改用 <物件參照>.<變數名稱> 的方式來存取。

## 靜態屬性變數 (Class Variables、Static Fields)
* 靜態變數在類別檔載入時就已經初始化，早過  
    1.任何該類別的物件被建立（new）  
    2.任何該類別的靜態方法。
* 有預設值, 如果沒有給值, 編譯器也會給予預設值（數字型態為0、字元型態為NULL、布林型態為false）

<hr>


#  stack && heap 記憶體
* stack 堆疊區，後進先出LIFO(last in, first out)，儲存區域變數。
* heap 堆積區，儲存實體物件，Java中會有Garbage Collector機制清除。
* **區域變數**放在 stack 區，執行時期動態生滅。
* **實體變數**放在 heap 區，隨實體存亡而生滅。
* **靜態變數**放在 global 區，編譯成功時即存在，程式結束時消滅。 

來源：yhhuang1966.blogspot.tw/2014/03/java_25.html)



<img src="https://i1.wp.com/img.alexleo.click/java-%e8%ae%8a%e6%95%b8%e7%9a%84%e7%af%84%e5%9c%8d%e8%88%87%e9%a1%9e%e5%9e%8b/java-memory.jpg?ssl=1" width="50%" height="50%" />