# 快捷鍵
## 使用上一個參數 
`ESC` 再按  `.` 

## 使用上一個指令
```sh
# 使用上一個指令
!$

# 使用上一個 d 開頭的指令
!d  
```

## 移動命令行游標
* 移到最前面: `Ctrl` + `a`
* 移到最後面: `Ctrl` + `e`

## 刪除命令行文字
* 刪掉游標前所有文字: `Ctrl` + `u`
* 刪掉游標後所有文字: `Ctrl` + `k`

## 查看上一個指令執行結果狀態碼
使用 `echo $?`
* 成功: 0
* 失敗: 1-255

<br>

<br/>

# 系統管理指令

## hostname
查看主機名稱
```sh
hostname
```

## df (disk free)
查看硬碟空間
```sh
df
```

## du (disk usage)
查看目錄內所有檔案使用的硬碟空間
```sh
du

# -h --human-readable 更換單位
# -m --megabytes 以 MB 為單位
```


## free 
查看記憶體狀態
```sh
free
```

## top
查看目前系統服務項目 (類似 windows 工作管理員)，ctrl + c 離開
```sh
top
```

## who
查看系統上的使用者。  
`who` 和 ` w` 功能基本相同，`who` 僅列出 user 和 登入時間。
```sh
who
# -b --boot 系統啟動時間
# -r --runlevel 運行級別


# 同 who，多了檢視時間 
w
```

## tty
查看目前終端設備
```sh
tty
```

## shutdown
會將系統服務關閉後才關機，比`poweroff`和`halt`安全。

```sh
# 語法
shutdown [-t seconds] [-rkhncfF] time [message]

# 立馬關機
shutdown now

# 指定時間關機
shutdown 21:30       # 指定幾點幾分
shutdown +10         # 指定 10 分鐘後

# 取消關機 (將指定關機取消)
shutdown -c

# 重新開機
shutdown -r

# 關機後停機
shutdown -h
```

## halt poweroff reboot
關機 重啟
```sh
# 關機
poweroff
# 重啟
reboot
```


## su
進入 super user 模式，需要輸入 root 密碼。
```sh
su
```
設定 root 密碼
```sh
sudo passwd root
```
切換用戶 switch user
```sh
# 切換
su -l USERNAME

# 切換成root用戶
su -
```
```sh
# 進入sudo權限
sudo -i
```

<br/>

<br/>

`su -` 和 `sudo -i` 差異:
* `su -` 是當前用戶直接切換成 root 用戶，需要輸入 `root 用戶密碼`。

* `sudo -i` 進入當前用戶的 sudo 權限，需要輸入`當前用戶密碼`，前提是`當前用戶要被加入 sudo 權限的群組`。

<br/>

<br/>

```sh
# 退出
exit
```

## passwd
更改登入密碼
```sh
passwd
```

<br/>

<br/>




# 網路功能指令

## ifconfig
查詢目前系統網路狀況，若不行使用則用`ip addr`
```sh
ifconfig
```

## route
查詢網路路由表
```sh
route
```

## netstat
查詢網路狀況
```sh
netstat
```

## ping
ping ICMP封包至目標網路 
```sh
ping 8.8.8.8
```

## nslookup
查詢目標網址的 IP
```sh
nslookup www.google.com.tw
```

## traceroute
查詢封包傳送狀態
```sh
traceroute
```

<br/>

<br/>

# 時間指令

## 硬體時間 系統時間
* 硬體時間: 指主機板上的時鐘裝置。  
* 系統時間: 指 kernel 的時鐘。當 Linux 啟動時，系統時間會去讀取硬體時間的設定，之後系統時間即獨立運作。

## date
查看系統時間
```sh
# date [option] [+format]
# 顯示日期
date +%F    # 2021-01-01
date +%D    # 01/01/21

# 顯示時間，有空格就加引號
date +%T
date "+%T %D"

# 顯示格式化時間
date "+%Y-%m-%d %H:%M:%S"

## timestamp 秒數
date +%s
```
修改系統時間，要去 timedatectl 將 ntp 關閉才能改
```sh
## CC 為幾世紀
date [MMDDhhmm[[CC]YY][.ss]]
```

查看硬體時間
```sh
hwclock
```
修改硬體時間
```sh
hwclock --set --date "2021-01-01 00:00:00"
```
同步時間
```sh
# hardware clock to system 將硬體時間複製到系統時間 (改成硬體時間)
# 需先關閉 timedatectl 的 ntp
hwclock --hctosys
hwclock -s

# system to hardware clock 將系統時間複製到硬體時間 (改成系統時間)
hwclock --systohc
hwclock -w
```


## cal
查看當月日曆
```sh
cal
```
查看整年年曆
```
cal 2021
```

## timedatectl
查看系統時間資訊
```sh
timedatectl
```
設定時區
```sh
timedatectl set-timezone "Asia/Taipei"
```
設定時區 (使用 GUI)
```sh
dpkg-reconfigure tzdata
```

設定系統時間
```sh
timedatectl set-time "2021-01-01"
```
如果系統有設定以 ntp 自動校時，在手動更改日期與時間時，就出現這樣的錯誤訊息： `Failed to set time: Automatic time synchronization is enabled`

所以要先將 ntp (Network Time Protoco) 關閉
```sh
# 關閉
timedatectl set-ntp no

# 打開
timedatectl set-ntp yes
```

<br/>

<br/>

# "指令"相關指令

## grep 
global search regular expression(RE) and print out the line。  
查詢某些特定字元，篩選出需要的資訊
```sh
ps -aux|grep sendmail       # 在 ps -aux 指令中，篩選出有關鍵字 sendmail 的資訊

man ls|grep later           # 篩選在 ls 手冊中有含關鍵字 later 
```

## type
顯示命令類型
```sh
type su
```


## man 
查看該指令的操作手冊，分章節:
1. 用戶命令 (/bin, /usr/bin, /usr/local/bin)
2. 系統調用
3. lib 用戶
4. 特殊文件 (設備文件)
5. 文件格式 (配置文件的語法)
6. 遊戲
7. 雜項 (miscellaneous)
8. 管理命令 (/sbin, /usr/sbin, /usr/local/sbin)
9. kernel routines

```sh
# man <章節> <指令>
man 4 tty
```
一次列出所有章節
```sh
# man -aw <指令>
man -aw tty
```


每個章節的大綱，又分為:
* NAME: 名稱
* SYNOPSIS: 語法
* DESCRIPTION: 詳盡說明各選項功能
* OPTIONS: 說明選項意義
* FILES: 相關配置文件
* BUGS:
* EXAMPLES: 範例
* SEE ALSO: 另外參照

翻頁指令:  
* `空白鍵`: 下一頁
* `b`: 上一頁
* `Ctrl+d`: 向上半頁
* `Ctrl+u`: 向下半頁
* `g`: 跳至第一行
* `G`: 跳至最後一行
* `數字+g`: 跳至第幾行

搜索關鍵字
* 查找不區分大小寫
* `/keyword`: 從上開始找
* `?keyword`: 從下開始找
* `n`: 下一筆資料
* `N`:上一筆資料

## info 
查詢命令線上文檔
```sh
info [command]
```

## alias unalias
查看/自行定義別名；僅對當前 shell 有效
```sh
# 查看
alias

# 定義
alias NAME="COMMAND"    # alias cls="clear"

# 刪除別名
unalias NAME            # unalias cls
```

<br/>

<br/>

# 輸出指令

## echo 
印出文本
```sh
echo [string]

# 使用跳脫字元選項 `-e`
echo -e "123\n123"

# 不自動換行
echo -n "123"
```

## printf
與 ehco 相比，不會自動換行
```sh
$ echo "Hello, Shell"
# Hello, Shell

$ printf "Hello, Shell\n"
# Hello, Shell
```

## echo $SHELL
查看當前 shell 類型
```sh
echo $SHELL
```

## 雙引號 單引號
```sh
# 雙引號，弱引用，可以使用變數
echo "$SHELL"       # /bin/bash

# 單引號，強引用
echo '$SHELL'       # $SHELL

# {} 變數正規符號
echo ${SHELL}
```

<br/>

<br/>

## tar

選項

* -c : 建立新的檔案
* -x : 從tar檔案中提取檔案
* -v : 顯示詳細資訊
* -f : 指定輸出tar檔案的名稱
* -z : 使用 gzip 壓縮目標文件
* -j : 使用 bzip2 壓縮目標文件
* -J : 使用 xz 壓縮目標文件
* -C : 指定解壓縮的目標路徑

### 創建 tar 包

```sh
# 創建不壓縮的 tar 包
tar -cvf new_test.jar.tar /tmp/test.jar

# 創建壓縮的 tar 包
tar -czvf new_test.jar.tar.gz /tmp/test.jar
```

<br/>

### 解開 tar 包
```sh
# 解開不壓縮的 tar 包，並指定路徑
tar -xvf new_test.jar.tar -C /path/to/new_test.jar

# 解開gzip壓縮的 tar 包，並指定路徑
tar -xzvf new_test.jar.tar.gz -C /path/to/new_test.jar
```