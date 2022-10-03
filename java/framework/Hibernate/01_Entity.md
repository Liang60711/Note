## 實體類規則

1. 屬性是 `private` 
2. getter 和 setter 為 `public`
3. 需有一個屬性為唯一值，一般使用 id 值
4. 實體類屬性建議不使用基本數據類型(`int`)，而使用數據類型對應的封裝類(`Integer`)。

    ```java
    // 舉例: 表示學生的分數

    // 使用 Integer
    Integer score = 0;      // 學生考0分
    Integer score = null;   // 學生沒參加考試

    // 使用 int
    int score = 0           // 不能區分學生是 考0分 還是 沒參加考試
    int score = null;       // int 不能為 null，會報錯
    ```

<br/>

<br/>

## 主鍵生成策略

|strategy|說明|
|--|--|
|increment|PK自動加1|
|identity|PK自動加1，但需要SQL是有支持的，如 `ORACLE`  就不支持|
|sequence|需要支持序列的SQL，如 `ORACLE` 才有支持|
|*native|自動系統選擇，不用擔心要選 identity 還是 sequence |
|*uuid|會自動生成 uuid，但要注意Entity中的 id 屬性類型須改為 `String`|

<br/>

<br/>

