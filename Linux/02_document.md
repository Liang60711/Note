# 檔案類型

## 概念/規則
1. 嚴格區分大小寫。
2. 目錄也是檔案 (文件)。
3. 同一個路徑下，兩個檔案不能同名。
4. 檔案名不可使用 ` / `；最長不能超過 255 字母；檔案開頭以`.`開頭為隱藏檔案。
5. dirname 和 basename，以` /etc/sysconfig/nwrwork-scripts/ifcfg-eno16777736 ` 為例。
    * basename: 最右側的檔案或目錄名 `ifcfg-eno16777736`。
    * dirname: basename 左側的路徑 `/etc/sysconfig/nwrwork-scripts `。

## 詳細資訊
輸入 `ls -l` 
```sh
# 取隨便一個檔案為例
drwx------ 2 liang liang 4096 6月 10 15:41 ssh-xxxxxxxxx
```

* 檔案以 10 個字母定義，開頭第 1 個字母為檔案類型 
    * `-` 普通檔案，即`f`
    * `d` 目錄檔案；為路徑映射，非 windows 檔案夾概念。
    * `b` 塊(隨機)裝置檔案 (block device)；可以隨機存取的設備，如硬碟、光碟機。
    * `c` 字元(線性)裝置檔案 (character device)；依照先後順序存取資料的設備，如印表機；終端機。
        * major number 主設備號: 標示設備類型。
        * minor number 次設備號: 同一類型中不同的設備。
    * `l` 符號鏈接檔案 (symbolic link file)；軟鏈接檔案。
    * `p` 命名管道檔案 (pipe)
    * `s` 套接字檔案 (socket)

* 後 9 個字母為檔案權限，每 3 位一組 (檔案所有者權限、用戶組權限、其他用戶權限)，每 1 組 rwx 為
    * `r`: 讀取
    * `w`: 寫
    * `x`: 執行

* 數字: 檔案硬鏈接的次數: 2

* 檔案的所有者 (owner): liang

* 檔案的用戶組 (group): liang

* 檔案大小 (size)，單位是字節: 4096

* 時間戳 (timestamp)，最近一次被修改的時間  

* 檔案名稱


## Data 和 Metadata
Data: 檔案本身的資料
```sh
cat FILE
```
Metadata: 用來描述檔案資料的資料，如檔案名稱、修改時間等。
```sh
stat FILE
```


## 檔案種類 (以外部來看)
1. 二進制檔案
2. lib 檔案
3. 配置檔案
4. 幫助檔案


## FHS (Filesystem Hierarchy Standard)
* `/bin`: 所有用戶可用的基本命令檔案。
* `/sbin`: 供系統管理使用的工具程式。
* `/boot`: Linux 核心和開機相關檔案，如 kernel、initramfs(initrd)、grub。
* `/dev`: 設備(裝置)檔案或特殊檔案。
* `/etc`: 系統程式的配置檔案。
* `/home`: 普通用戶家目錄的集中位置；預設路徑 `/home/USERNAME`。
* `/root`: root 的家目錄。
* `/lib`: 為系統啟動或根檔案系統上的應用程式提供共享 lib，以及為 kernel 提供 kernel model。
    * `/libc.so.*`: 動態鏈接的 C lib。
    * `/ld*`: 執行時鏈接器。
    * `/modules`: 用於儲存 kernel model。
* `/lib64`: 64bit 系統共享 lib。
* `/media`: 外接式設備，掛載點。
* `/mnt`: 其他檔案系統的臨時掛載點。
* `/opt`: 附加應用程式的安裝位置，可選路徑。
* `/proc`: 虛擬檔案系統，不佔任何硬碟空間。
* `/sys`: 虛擬檔案系統，較`proc`新。

* `/tmp`: 臨時檔案；所有用戶都可以使用。
* `/usr`: 此目錄包括許多子目錄，用來存放系統指令、安裝程式及套件。
    * `/usr/local`: 讓系統管理員安裝應用程式、安裝第三方程式(舊版本會安裝在 `/opt`)。
* `/var`: 系統執行中，常態性變動的檔案。

<br/>

<br/>


# 查看類指令

## basename
查詢 basename
```sh
basename /etc/sysconfig/nwrwork-scripts/ifcfg-eno16777736

# ifcfg-eno16777736
```

## dirname
查詢 dirname 
```sh
dirname /etc/sysconfig/nwrwork-scripts/ifcfg-eno16777736

# /etc/sysconfig/nwrwork-scripts
```

## ls
list
```sh
ls -l           # 顯示詳細資料
ls -a           # 顯示隱藏檔 (以"."開頭的檔案)
ls -A           # 與 -a 相同，少了 . 和 .. 兩個檔案
ls -h           # 做單位轉換
ls -d           # 僅列出目錄本身，而不是列出目錄內的檔案資料(常用)
ls -i           # 顯示 inode (index node)
ls -r           # 反向排序檔案
ls -R           # 遞迴列出所有子目錄的檔案
ls -al|more     # 將檔案內容以一頁一頁顯示
ls -al /etc|grep yum   # /etc 中找關鍵字 yum 的檔案
ls -ltr         # 檔案依照時間排序，讓最新的檔案排在最後
```

## pwd
printing working directory
```sh
pwd             # 顯示當前路徑
```

## cd
change directory
```sh
cd              # 家目錄
cd ~            # 家目錄 (同上)
cd ~USERNAME    # 切換至指定用戶的家目錄
cd -            # 往返目錄 (遙控器上的返回鍵)
```

## cat
concatenate (連接)，連接檔案並 print 出來，只能印出文字檔案；可以先用 `file` 查看是否為文字檔案
```sh
# 單個檔案
cat FILE

# 可多檔案一同查看
cat FILE_1 FILE_2

# 增加行號(常用)
cat -n FILE
```

## tac
與`cat`相同，只是倒著顯示。
```sh
tac FILE
```


## file
查看檔案類型
```sh
file FILE
```


## more
一頁一頁查看
```sh
more FILE
```

## less
與`more`類似，多了一個向前翻頁的功能
```sh
# 1
less FILE

# 2
man ls|less
```

## head
預設是 10 行
```sh
# 只看檔案前幾行
head FILE

# 更改行數至 20 行
head -n 20 FILE

# 查看檔案前 10 行
cat FILE|head

# 查看檔案前 n 行
cat FILE|head -n
```

## tail
預設是 10 行，一樣可以更改行數
```sh
# 只看檔案後幾行
tail FILE

# 查看檔案尾部後不退出，進入監控狀態
tail -f FILE
```

## stat
查看檔案相關資訊，每個檔案都有 3 個 timestamp (存取時間、資料修改時間、狀態修改時間)
```sh
# stat DIR
# stat FILE

# access time：2021-06-16 14:15:05.660007370 +0800
# modify time：2021-06-09 17:41:29.636095721 +0800
# change time：2021-06-09 17:41:29.636095721 +0800
```
可以使用 `touch` 來更改這 3 個 timestamp
```sh
# 查看 metadata
stat /root/.bash_history

# 更改 3 個 timestamp 至最新
touch /root/.bash_history

# -a 僅修改 access time
# -m 僅修改 modify time
# -t STAMP [[CC]YY]MMDDhhmm[.ss] 指定修改時間
touch -t 202101010000.00 /root/.bash_history
```

## touch
可以更改 timestamp 和 建立空檔案。
```sh
# 檔案不存在，就會建立一個新檔案
touch /tmp/hello

# -c 即使檔案不存在，也不建立新檔案
touch -c /tmp/hello
```


<br/>

<br/>


# 目錄檔案相關指令

## tree
查看檔案樹狀結構
```sh
tree DIRECTORY

# 只顯示層級 1 檔案
tree -L 1 /tmp
```



## mkdir
創建目錄時，需要確保 dirname 已經存在，才能建立 basename。
```sh
# 在 z 建立之前，需要確保 /tmp/x/y 路徑都已經存在。
mkdir /tmp/x/y/z
```
所以較快的方式是 `-p`、`--parents`，可以一次建立所有尚未建立的目錄。
```sh
# 順序為建立 x >> y >> z
mkdir -p /tmp/x/y/z
```
顯示詳細過程
```sh
mkdir -pv /tmp/x/y/z
```

## rmdir
只能刪除空目錄
```sh
mkdir test      # 移除 test 目錄，如果內有檔案無法刪除
```
## rm -rf
建議不用的檔案不要直接刪除，而是移動到某個專用目錄。
```sh
# -r 遞迴
# -f 強制
rm -rf test     # 強制刪除
```



## cp
* 單檔案複製: `cp [OPTION]...[-T] SOURCE DEST`
    * 若 DEST 不存在: 會建立新檔案，並複製到 DEST
    * 若 DEST 存在: 
        * DEST 非目錄: 覆蓋原檔案(無法恢復)。
        * DEST 是目錄: 在目錄下建立同名檔案，並複製到 DEST 中。
* 多檔案複製: `cp [OPTION]...SOURCE...DIRECTORY`
    * 若 DIRECTORY 不存在，錯誤
    * 若 DIRECTORY 存在:
        * DIRECTORY 非目錄: 錯誤。
        * DIRECTORY 是目錄: 分別複製每個同名檔案。

* 多檔案複製(倒過來寫): `cp [OPTION]...-t DIRECTORY SOURCE...`  

```sh
# -i --interactive 若有覆蓋動作會先提示
# -f --force 強制覆蓋目標檔案
# -r --recursive 複製目錄及內容檔案
cp -r /var/log /tmp/
```

## mv
與 `cp` 邏輯相同
```sh
mv .bashrc /    # 將 .bashrc 檔案移至根目錄
mv /.bashrc .   # 將 /.bashrc 移至目前目錄
```


## ln

> 圖解 https://tw511.com/a/01/42845.html

連結 (link) 可以視為檔案的別名，分為:
* 軟連結 (symbolic link):
    1. 以路徑型式存在，類似 windows 捷徑。 
    2. 可跨越檔案系統。
    3. 可對一個不存在的檔案進行連結。
    4. 可對目錄進行連結。
* 硬連結 (hard link):  
    1. 與元檔案指向同一個inode，修改任意一個檔案，另外一個檔案的內容也會同步變化。
    2. 除了檔名不一樣之外，硬連結建立的檔案跟原始檔的所有元資訊完全一樣(硬連結的檔案都是互相等價的，互為硬連結)。
    3. 不可跨越檔案系統。
    4. 不可對目錄進行連結。
    5. ln 指令預設使用硬連結。
    6. 刪除副本檔案也不會造成原檔案也跟著刪除，因此硬連結等於是建立一個不用容量備份。

`ln -s <來源檔案(目錄)> <目的檔案(目錄)>`
```sh
ln -s /usr/bin bin      # -s 為建立 symbolic 連結
```

## find 
可搜尋整個文件目錄的**檔案**，預設從根目錄開始搜尋，功能強大但速度慢，可以指定範圍。  
`find <路徑> -name <檔名>`
```sh
find / -name bin        # 在跟目錄中尋找名稱為 bin 的檔案
```
搭配 globbing
```sh
# 當前目錄查找 my 開頭檔案
find . -name "my*"
```


## whereis
搜尋所有**檔案**或**指令**，利用曾經找過的系統內的資訊找檔案，速度較快，找不到並不代表不存在。
```sh
# 檔案
whereis FILE

# 指令(指令也是檔案)
whereis COMMAND

# -b 查找二進制檔案
```

## which
找的是**可執行**的**檔案**(指令就是可執行的檔案)，指令基本都在`$PATH`中搜尋，查找範圍最小，速度最快，預設只會返回第一個找到的路徑。
```sh
# 
which COMMAND

# 找所有不只第一筆
which -a COMMAND
```


<br/>

<br/>

# 壓縮檔案指令
## compress
將檔案壓縮成副檔名為 .Z 的檔案
```sh
compress test123                # 壓縮檔案名 test123.Z
```
自訂壓縮檔案名稱
```sh
compress test123 -d xxxx.Z      # 壓縮檔案名 xxxx.Z
```

## gzip
同上，壓縮成副檔名為 .gz 
```sh
gzip test123
```
自訂壓縮檔案名稱
```sh
gzip -d test122 xxxx.gz
```

<br/>

<br/>