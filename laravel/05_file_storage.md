# 圖片檔案儲存
### 通常儲存圖片時，有兩個選擇:

### 1. 儲存在 public 目錄
* 直接在將檔案放在 public/images/ 位置，再用 URL() 函數去讀取。  
* 補充: 因為 public 目錄權限存取為公開的。所以所有的 css, javascript, image 都可被存取，比較簡單的方式。

<br/>

<br/>

### 2. 儲存在 storage 目錄
* 因為 storage 權限為不公開的，所以取資料時需要有一個從 public 指向 storage 的連結 (**symbolic link**)。
* 優點: 相對安全，也可以限制訪問 storage 目錄的 user，例如: 個人的大頭貼照只允許我自己存取。
1. 建立指向 storage/app/public 連結。

    ```php
    $ php artisan storage:link
    ```
    指令會建立一個 public/storage 目錄，並指向 
    ```
    public/storage（symbolic link） → storage/app/public
    ```
2. 將圖片檔案存入 public/storage 目錄中。
3. 在模板中撈圖片檔，可使用 URL() 或 asset 抓取。

    ```php
    <a href="{{ URL('storage/apple.jpg') }}">images</a>
    ```
    ```php
    <a href="{{ asset('storage/apple.jpg') }}">create</a>
    ```