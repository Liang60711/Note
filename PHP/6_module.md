# require() 和 include()

### 匯入檔
* 匯入檔的副檔名 通常是 <code>.inc</code>。
* 匯入檔案的路徑使用字串。

    ```php
    require('./test.inc');
    include('./test.inc');
    ```

### require()
較嚴格的載入函數，若沒有匯入檔，則會報錯。

<br/>

### require_once()
已匯入過相同檔案，就不會再進行匯入，但如果檔案已經匯入過會報錯 (function can not redeclare)。

<br/>

### include()
較寬鬆，若沒有匯入檔，不會抱錯，直接 pass。

<br/>

### include_once()
已匯入過相同檔案，就不會再進行匯入。
