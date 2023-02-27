# 參考 Reference
[鳥哥的linux私房菜](https://linux.vbird.org/)

<br/>

<br/>

# 名詞解釋
## CPU 中央處理器 五大單元
* 控制單元 (CU): 負責控制資料和指令的單元，並且還要控制五大單元之間的所有操作流程。
* 算術邏輯單元 (ALU): 運算與判斷，例如：加減乘除數學運算，以及 AND、OR、NOT、XOR 邏輯運算。
* 暫存器 (Register): 用來存放剛運算完畢的資料或正在運算中的資料。

* 快取記憶體 (Cache): 
    * CPU 內部暫存器上的資料和指令需要和主記憶體做交換，但是暫存器和主記憶體的速度差很多，會造成CPU一直在等待主記憶體傳送和接收資料，浪費CPU的資源。
    * 因此架構上設計了中間緩衝的元件，叫做快取記憶體 (Cache)，速度介於暫存器和主記憶體，存放最近被CPU存取過或常用的資料或指令。

* 內部匯流排 (Bus): CPU 元件中，控制單元會先讀取指令並瞭解指令，再將資料放進暫存器中，接著通知算術邏輯單元，針對暫存器中的資料作運算，再將運算完畢的資料放回暫存器中。最後再將資料回存到主記憶體或硬碟中，中間透過匯流排來傳遞資料。


## 32 位元系統
CPU 中，暫存器 (Register) 的數據寬度為 32 位元；代表此 CPU 一次可以處理 32bits 的資料；最多能使用 4GB  記憶體。

## 組合語言 (彙編語言， Assembly language)
為低階語言，是一種與硬體有著密切關係的低階語言，並且根據 CPU ，組合語言的語法也不相同。

## 組譯器 (Assembler)
由於 CPU 只認得機器碼，因此組合語言所撰寫的程式依然需要經由組譯器 (Assembler) 來轉譯為機器碼；組譯器用來將組合語言使用的原始檔 (.asm) 翻譯成含有機器碼的二進制目的碼 (Object Code)。

* 各種語言的文氏圖

    > https://rahul77349.medium.com/machine-code-vs-byte-code-vs-object-code-vs-source-code-vs-assembly-code-812c9780f24c

## GPL 協定
General Public License，是一個廣泛被使用的自由軟體許可協議條款。

## GNU/Linux
GNU 提供 Application、開源程式碼；Linux 提供 Kernel，兩者結合成現在的 Linux。

## kernel
將硬體的計算能力及資源提供(虛擬化)給上層的程序 (process) 使用，為每個程序建立一個虛擬的獨立空間 (各程序之間不會互相干擾)。

```sh
                # 上層
                Application
                |    |
System call<----|    |--> Library call
                |    |
                |  Library(API)
                |    |
                |    |      
                |    |
                Kernel
                |
                |
                Physical facilities (硬體設備)
                # 底層
```

## library
kernel 的 system call 過於底層，所以造成系統開發難度高，故將一個或多個 system call 的功能封裝成較高級的 library API，不能單獨執行，只能被調用。


## .dll
Windows 的動態連結函式庫 (Dynamic link library) 檔案。

## .so 文件
Linux 的動態連結函式庫 (Shared object) 檔案。

## FHS
檔案系統階層標準 (Filesystem Hierarchy Standard)，定義 Linux 作業系統中的主要目錄及目錄內容，任何系統都必須遵循這套標準。

## Linux 三大分支
* 三大分支，有些再進行二次分支。
1. Debian (沒有商業公司支持，但最自由): 
    * ubuntu
    * knopix (資安著稱)
    * mint (圖形介面著稱)
2. Slackware:
    * S.u.S.E.
3. Redhat: 
    * Redhat 9.0: 
        * RHEL (Redhat Enterprise Linux)
        * CentOS (Community ENTerprise OS，RHEL 社群版)
        * Fedora Core (每6個月更新)


## 編譯方式
* Linux 的 Library 由 glibc 所提供。
1. 動態(鏈接)編譯: 撰寫程式時，不將 lib model 寫進入程式中；執行程式時會調用 lib model；優點是節省記憶體資源。
2. 靜態編譯: 撰寫程式就將 lib model 複製進程式內部中；優點是不管到哪個系統中，都可以執行，不用擔心沒有該 model。


<br/>

<br/>

# 基本原則
1. 複雜任務，都是由小程序組合而成，一個程序只做一件事。
2. 一切皆文件。
3. 盡量避免跟用戶交互。
4. 配置文件為文本格式。
5. CLI:  
    * 命令提示符 (prompt) 分為
        * #: root
        * $: 普通用戶
    
    * 命令格式: 為 **命令 command**，**選項 options**，**參數 arguments**

        ```sh
        # ls為命令 -l為選項 /ect為參數
        ls -l /ect
        ```
6. Shell 種類:
    * GUI:
        1. Gnome
        2. KDE
    * CLI:
        1. bash (功能最齊全)
        2. zsh
        3. csh
        4. ksh
        5. tcsh

7. 虛擬terminal: 
    * 查看虛擬終端

        ```sh
        tty
        ```
    * 更改虛擬終端   
        可以使用快捷鍵 `Ctrl` + `Alt` + `F1~F6`，或是使用指令

        ```sh
        # 切換到 tty3
        # change virtual terminal 
        chvt 3
        ```

    * 有些圖形化介面的 Linux 版本 `F1~F2` 為系統佔用。


<br>

<br/>


# 指令相關規則

## 查詢命令類型
輸入 `type COMMAND` 指令來查看命令類型。
1. 內建命令: shell built-in，是 shell 的一部分，通常在運行 Linux 時，就會被載入在記憶體中。
2. 外部命令: 因為使用上太佔記憶體，在系統載入時，不一定會一起被載入到記憶體中，需要時再調用；**在文件系統的某個路徑下，會有一個與命令相同的執行文件**。
3. 命令可以有別名:
    * 別名可以與原名相同，此時原名被隱藏；如 `ls` 為 `ls --color=auto` 的原名。
    * 如果要使用原名使用 `\ls`
    * 查看/定義別名使用 `alias`；只對當前 shell 有效。


## 查詢環境變數
輸入 `printenv`，會找到 $PATH。
```sh
# 路徑使用 : 隔開 代表尋找文件的優先順序
PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
```
直接印出也可以
```sh
echo $PATH
```

## hash 暫存
輸入 `hash`，查詢命令暫存表。

系统下會有 hash 表，每個 shell 獨立，每當執行命令時，hash 表會記錄命令的路徑。第一次執行命令 shell 會從 PATH 路徑下找該命令的路徑，當第二次使用該命令時，shell 會查看 hash 表，沒有該命令才會去 PATH 路徑下尋找。

hash 表作用: 提高命令的調用速度 (cache is king)。
