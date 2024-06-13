# 用戶、群組、權限管理

## 基礎
* 3A
    * 驗證 (Authentication): 認證用戶為其所宣稱的人的動作。 
    * 授權 (Authorization): 授與已驗證的合作對象權限以執行某些工作的動作 (可以做到什麼程度)。
    * 紀錄 (Accounting): 紀錄此用戶做了甚麼事情；包括量測 (measuring)、監控 (monitoring)、報告 (reporting) 各種資源使用量及事件紀錄（log）。



## 用戶
* 用戶 (user): 每個使用者即是用戶。
* 種類: 
    1. 管理員
    2. 普通用戶: 分為**系統用戶**及**登錄用戶**

* 用戶 ID: 稱為 **UID**；16 bits 二進制 (0-65535)。
    1. 管理員 ID: `0`
    2. 普通用戶 ID: `1-65535`
        * 系統用戶 ID: `1-999`
        * 登錄用戶 ID: `1000-60000`
    
    * 名稱解析: 轉換 username 和 uid，需要有查詢表，位置在 `/etc/passwd`。

    * `/etc/passwd` 用戶查詢表: 
        * 格式: `name:password:UID:GID:GECOS:directory:shell`
            * `name`: 用戶名稱
            * `password`: 可以是加密密碼，也可以是佔位符`x`
            * `UID`: 用戶 ID
            * `GID`: 群組 ID
            * `GECOS`: 備註 comment
            * `directory`: 用戶家目錄
            * `shell`: 用戶預設 shell

<br/>

## 群組
* 群組 (group): 為一個容器，可將有權限相同的用戶歸類至此群組，此群組內的所有用戶彼此權限相同，類似 RBAC 權限架構的`角色`。
* 種類:
    1. 管理員群組
    2. 普通用戶群組: 分為**系統群組**及**登錄群組**

* 群組 ID: 稱為 **GID**；16 bits 二進制 (0-65535)。
    1. 管理群組 ID: `0`
    2. 普通群組 ID: `1-65535`
        * 系統群組 ID: `1-999`
        * 登錄群組 ID: `1000-60000`
    
    * 名稱解析: 轉換 GROUP_NAME 和 gid `/etc/group`。
        * 格式: `group_name:password:GID:user_list`


## 密碼認證
* 通過事先儲存的密碼，與登錄時提供的密碼進行比對。
* 早期密碼儲存位置同 uid 查詢表中 (`/etc/passwd`)，後來因安全問題改至
    * 用戶密碼: `/etc/shadow`。
        * 格式: `用戶名:加密的密碼:最近一次修改密碼的時間:最短使用期限:最長使用期限:警告期:過期日期:保留字串`
            * `最短使用期限`: 修改密碼的冷卻時間。
            * `最長使用期限`: 密碼使用超過此時間，需進行更換。
            * `警告期`: 密碼到期之前提醒的時間
            * `過期日期`: 可自行設定密碼過期日期

    * 群組密碼: `/etc/gshadow`。

* [加密算法](https://github.com/Liang60711/Note/blob/67087d3e3b2b3f70313359e635eb9e1cfec77e67/Basic/0_osi.md#ssl-%E5%8D%94%E5%AE%9A):
    1. 對稱加密: plain text 加密為 cipher text。
    2. 非對稱加密。

    * 雜湊 (hash) 算法:
        * `md5`: message digest；128 bits。
        * `sha` :secure hash algorithm；160 bits。

            ```sh
            # md5sum
            echo 'hash,hash' | md5sum   # 產出 hash 碼

            # sha512sum
            echo 'sha512sum' | sha512sum
            ```
    
    * 如果密碼相同，而產出相同的 hash，同樣有風險，故在進行 hash 算法前，會加入 salt (隨機數)，確保 hash 會不一樣。

<br/>

<br/>

# 命令
## 查看用戶、用戶組
```sh
# 查看用戶
cat /etc/passwd | tail -5

# 查看用戶組
cat /etc/group | tail -5
```


## groupadd
建立一個新用戶組別，`groupadd [OPTION] GROUP_NAME`
```sh
groupadd GROUP_NAME

# -g 手動指定GID
groupadd -g 1234 GROUP_NAME

# -r 建立系統組
groupadd -r GROUP_NAME
```
## groupmod
修改用戶組別屬性，`groupmod [OPTION] GROUP_NAME`
```sh
# -g 修改 GID
groupmod -g 1234 GROUP_NAME

# -n 修改新祖名
groupmod -n NEW_NAME GROUP_NAME
```

## groupdel
刪除用戶組別
```sh
groupdel GROUP_NAME
```



## useradd
建立新用戶，`useradd [OPTION] USER_NAME`  
若用戶建立時未指定組別，則會自動建立同名的 group
```sh
useradd USER_NAME

# -u --uid 指定用戶 UID
useradd -u 1234 USER_NAME

# -g, --gid 指定 用戶基本組ID，此組需要事先存在
useradd -g group_01 USER_NAME

# -G --groups 指定 用戶附加組ID，可多選，此組需要事先存在
useradd -G group_02 USER_NAME

# -s --shell 指定 shell種類，可選的 shell 可以在 /etc/shells 找到
cat /etc/shells

useradd -s /bin/bash USER_NAME

# -r --system 建立系統用戶
useradd -r USER_NAME
```
在建立 user 後，會自動建立一個 email 
```sh
ls /var/spool/mail
```
顯示建立用戶時的**預設設定**
```sh
# 查看預設值
useradd -D

# 輸出 #
# GROUP=100
# HOME=/home
# INACTIVE=-1
# EXPIRE=
# SHELL=/bin/bash
# SKEL=/etc/skel
# CREATE_MAIL_SPOOL=yes


# -D [OPTION]  將 sh 設為預設 shell
useradd -D -s /bin/sh
```

## usermod
修改用戶屬性
```sh
# -u --uid 修改用戶的 uid 
usermod -u 1234 USER_NAME

# -g --gid 修改用戶的基本組
# 基本組只能單選
usermod -g NEW_GROUP USER_NAME

# -G --groups 指定 用戶附加組ID，可多選，此組若事先存在(會覆蓋)
# 附加組可以多選
usermod -G GROUP_01, GROUP_02 USER_NAME

# -a --append 與-G一同使用，用戶追加新的附加組(不會覆蓋)
usermod -a -G GROUP_03 USER_NAME

# -d --home 修改用戶的家目錄，用戶原本在家目錄的文件不會被轉移至新位置
usermod -d /tmp USER_NAME

# -m --move-home 與-d一同使用，修改用戶的家目錄，並將所屬文件一同轉移至新位置
usermode -m -d /tmp USER_NAME
```

<br/>

<br/>

## 新增用戶流程

```sh
# 新增使用者
useradd {username}

# 查看使用者
id {username}

# 幫用戶新增密碼
passwd {username}

# 幫用戶新增組別(附加組)
# usermod 設定結束後，需要重新登入該用戶才會生效
usermod -aG {groupname} {username}
```

使用 `--shell` 選項來指定 shell 類型
```sh
# 新增使用者並指定 shell
useradd -s /bin/tcsh [username]

# 查看使用者 shell 類型
tail -1 /etc/passwd
```

<br/>

<br/>

## 刪除用戶流程

一般實際情況下，不會將家目錄刪除，會保留該用戶以前產生的資料。

```sh
# 刪除用戶，但保留家目錄
sudo userdel {username}

# 刪除用戶，一併刪除家目錄
sudo userdel -r {username}
```

<br/>

<br/>

## 修改用戶組
```sh
sudo usermod -g {group} {username}
```

<br/>

<br/>