## Tree

1. 二元樹: 任何節點左子節點數值較該節點小，右子節點數值較該節點大。
2. 平衡二元樹: 任何結點的兩個子樹高度差 <= 1
3. B Tree: 
    * 又稱為平衡多路查找樹，表示一個節點不只有2個子節點
    * 資料可以存在每一個節點
    * 所有葉節點都在同一層
    * 缺點: I/O不穩定(每個節點都有資料)；資料變大時，非葉節點會塞不下，導致節點儲存的資料筆數不同

        <img src='https://img-blog.csdn.net/20160202204827368'>

        <br/>

4. B+ Tree:

    * 非葉節點只存鍵值資料。
    * 使用鏈表，依序將葉節點串起來
    * 資料都存在葉節點中。
    * I/O較穩定，通通都在葉節點抓資料

        <img src='https://img-blog.csdn.net/20160202205105560'>

<br/>

<br/>

## Reference

> 各種Tree-csdn https://blog.csdn.net/yin767833376/article/details/81511377

> 各種Tree-ithome https://ithelp.ithome.com.tw/articles/10221111

<br/>

<br/>

## B+ Tree

> https://ithelp.ithome.com.tw/articles/10221111

<br/>

<br/>
