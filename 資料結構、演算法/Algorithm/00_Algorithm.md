# 排序 Sorting
>來源: http://spaces.isu.edu.tw/upload/18833/3/web/sorting.htm

## 穩定度
`穩定排序法(stable sorting)`: 如果鍵值相同之資料，在排序後相對位置與排序前相同時，稱穩定排序。

* 排序前：3,5,19,1,3*,10
* 排序後：1,3,3*,5,10,19  (因為兩個3, 3*的相對位置在排序前與後皆相同。)

`不穩定排序法(unstable sorting)`: 如果鍵值相同之資料，在排序後相對位置與排序前`不一定相同`或`不相同`時，稱不穩定排序。

* 排序前：3,5,19,1,3*,10
* 排序後：1,3*,3,5,10,19  (因為兩個3, 3*的相對位置在排序前與後不相同。)

<br/>

<br/>

## 內部/外部 排序
`內部排序(Internal Sort)`：所有排序操作都在記憶體中完成。

`外部排序(External Sort)`：由於數據太大，需透過其它儲存裝置輔助，
外部排序通常會分次載入部份的資料到記憶體，用內部排序演算法排序後再回存或合併結果。

<br/>

<br/>

## 時間/空間 複雜度
`時間複雜度(Time Complexity)`：一個演算法執行所耗費的時間。

`空間複雜度(Space Complexity)`：運行完一個程式所需記憶體的大小。

<br/>

<br/>

## 原地/非原地置換
`原地置換(In-place)`：使用資料原來的資料結構(陣列)進行排序，不需使用暫存的輔助資料結構，不佔用額外記憶體。

`非原地置換(Out-place)`：需使用暫存的輔助資料結構，佔用額外記憶體。

<br/>

<br/>

## 演算法比較
|排序方法|最壞時間|平均時間|穩定|額外空間|備註說明|
|--|--|--|--|--|--|
|氣泡排序 Bubble|O(n2)|O(n2)|穩定|O(1)|n小比較好。|
|選擇排序 Selection|O(n2)|O(n2)|不穩定|O(1)|n小較好，部份排序好更好。|
|插入排序 Insertion|O(n2)|O(n2)|穩定|O(1)|大部份排序好比較好。|
|快速排序 Quick|O(n2)|O(nlogn)|不穩定|O(n)~O(log n)|在資料已排序好時會產生最差狀況，`Arrays.sort()`是使用此排序|
|堆積排序 Heap|O(nlog2n)|O(nlog2n)|不穩定|O(1)||
|薛爾排序 shell| O(ns)<br/>1<s<2 | O(n(log2n)2) | 穩定 | O(1) | n小比較好。|
|合併排序 Merge|O(nlog2n)|O(nlog2n)|穩定|O(n)|常用於外部排序。|
|基數排序 Radix|O(nlogbB)|O(n)~O(nlogbk)|穩定|O(nb)|k:箱子數<br/>b:基數|

<br/>

<br/>

## 冒泡排序法 Bubble Sort
1. 每輪會產生一個最大值。
2. 每輪會兩兩比較元素大小。
3. 相同元素比較時，並沒有改變順序，為一種穩定的排序演算法。

```java
34 4 56 17 90 65 // 排序，由小到大
                 // 34和4比，向右交換位置，34和56比，不交換，56和17比，交換位置，56和90比，不交換，90和65比，交換位置。

4 34 17 56 65 90 // 第一輪，比較5次 (90已經排完，下輪不需要出現)


4 17 34 56 65    // 第二輪，比較4次 (90,65已經排完，下輪不需要出現)

4 17 34 56       // 第三輪，比較3次

4 17 34          // 第四輪，比較2次

4 17             // 第五輪，比較1次

```

冒泡排序寫法
```java
public static int[] bubbleSort(int[] nums){

    // 外迴圈算幾輪
    for(int i=0; i<nums.length-1; i++){    // 輪數等於陣列長度-1
        // 內迴圈算每輪比幾次
        for(int j=0; j<nums.length-1-i; j++){

            if (nums[j] > nums[j+1]){
                // 交換位置 34,4
                nums[j] = nums[j] + nums[j+1];  //38,4
                nums[j+1] = nums[j] - nums[j+1];//38,34
                nums[j] = nums[j] - nums[j+1];  //4,34
            }
        }
    }
    return nums;
}
```

<br/>

<br/>

## 選擇排序法 Selection Sort
1. 每輪選擇最大(最小)元素，放在已排好順序的數列最後，直到全部待排序的元素排序結束。
2. 每輪都也會兩兩比較大小，但跟冒泡排序不同的地方是，選擇排序法每輪只會交換一次元素位置。
3. 為一種不穩定的排序演算法。
4. 優點是不用占用額外的記憶體。

```java
34 4 56 17 90 65 // 排序，由小到大
                 // 第一輪中，4最小，排在最前面

4 34 56 17 90 65 // 第一輪，比較5次(4最小，順序和34換)
4 17 56 34 90 65 // 第二輪，比較4次(17最小，順序和34換)
4 17 34 56 90 65 // 第三輪，比較3次(34最小，順序和56換)
4 17 34 56 90 65 // 第四輪，比較2次(56最小，順序不變)
4 17 34 56 65 90 // 第五輪，比較1次(65最小，順序和90換)
```

選擇排序法
```java
public static int[] selectionSort(int[] nums){
    int minIndex = 0;
    // 外迴圈算幾輪
    for(int i=0; i<nums.length-1; i++){

        minIndex = i; // 每輪最小值的index(從尚未排序好的開始算)
        // 內迴圈算幾次
        for(int j=i+1; j< nums.length; j++){
            // 兩兩相比，找最小值的index
            if(nums[minIndex]>nums[j]){
                minIndex = j;
            }
        }
        // 如果最小值不是 i 這個index，就需交換兩元素
        if(minIndex != i){
            nums[minIndex] = nums[minIndex] + nums[i];
            nums[i] = nums[minIndex] - nums[i];
            nums[minIndex] = nums[minIndex] - nums[i];
        }
    }
    return nums;
}
```


<br/>

<br/>

## 插入排序法 Insertion Sort
> https://www.bilibili.com/video/BV1FK4y1x7Ny?p=31&spm_id_from=pageDriver

1. 由後向前找到合適的位置插入。
2. 每輪會用一個temp將該輪比較的元素記住。


```java
34 4 56 17 90 65 // 排序，由小到大

// 第一輪，起始位置index為1(判斷4是否要插入在34前面)
34 34 56 17 90 65   // 若需要插入，會先將4改成34，此時temp=4
4 34 / 56 17 90 65  // 再將首位改成4

// 第二輪，temp=56
4 34 56 / 17 90 65  // 56不用插入，所以值接下一輪

// 第三輪，temp=17
4 34 56 56 90 65    // 17比56小，將17改為56
4 34 34 56 90 65    // 17比34小，將56改為34
4 17 34 56 / 90 65

// 第四輪，temp=90
4 17 34 56 90 / 65  // 90不用插入

// 第五輪，temp=65
4 17 34 56 90 90    // 65比90小，將65改成90(90往後挪)
4 17 34 56 65 90
```
插入排序程式

```java
public  static  int[] insertingSort(int[] nums){
    // i為輪數，並且是index位置(i=1開始，從4這個數開始比)
    for(int i = 1; i< nums.length; i++){
        int temp = nums[i];
        int j = 0;
        // j為跟i比較的數
        for(j=i-1; j>=0; j--){  // j需要跟i以前的數比較，直到大於某個數後插入

            if(nums[j]>temp){
                nums[j+1] = nums[j]; // 將34,4改為34,34
            }else{
                break; // 若temp(即i的值)不用插入
            }
        }
        
        if(nums[j+1] != temp){
            // 將temp放回陣列中(34,34)=>(4,34)
            nums[j+1] = temp;
        }

    }

    return nums;
}
```

<br/>

<br/>

## 二分搜尋法 Binary Search
1. 必須確保數列是有排序的(由小到大)。
2. 輸入要查找的陣列、要查找的值，返回index，否則返回-1。
```java
int nums = {10,20,50,65,88,90};

binarySearch(nums, 50);
```
```java
public static int binarySearch(int[] nums, int key){    // nums是查找的陣列，key是查找的值(非index)
    int start = 0;
    int end = nums.length - 1;

    while(start <= end){
        int middle = (start + end) / 2;
        if (nums[middle] > key){
            end = middle - 1;
        }else if (nums[middle] < key){
            start = middle + 1;
        }else{
            return middle;
        }
    }

    return -1;
}
```