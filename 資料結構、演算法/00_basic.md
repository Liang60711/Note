## 資料結構種類
1. 集合結構
2. 線性結構
3. 樹形結構
4. 圖形結構


<br/>

<br/>

## 演算法目的
1. 時間複雜度低。
2. 空間複雜度低，可以理解為記憶體用量。

<br/>

<br/>

## 時間複雜度
1. 只與次方次數有正相關，與係數和常數無關(當n越來越大，係數可忽略)，例如: 1,2時間複雜度相同，3,4時間複雜度相同。
    * 2n^2
    * n^2
    * 2n+1
    * 3n+1

2. 時間複雜度應該以最壞的情況下去判定。

<br/>

<br/>

大O表示方法，通常演算法到平方級別，則可能需要對演算法進行優化:

|描述|大O表示方法|說明|舉例|
|--|--|--|--|
|常數級別|O(1)|普通語句，n不影響時間|將兩個數相加|
|對數級別|O(logN)|二分策略|二分查找|
|線性級別|O(N)|單迴圈|找出最大元素|
|線性對數級別|O(NlogN)|分治演算法|遞迴排序|
|平方級別|O(N^2)|雙迴圈||
|立方級別|O(N^3)|三迴圈||
|指數級別|O(2^N)|窮舉查找||


<br/>

<br/>

## 空間複雜度


<br/>

<br/>

## 參考資料

> 資結 https://clu.gitbook.io/data-structure-note/heap-tree

> 排序 https://clu.gitbook.io/data-structure-note/x-time-complexity-cheat-sheet