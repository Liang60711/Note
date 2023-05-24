## 設定 SSH
1. 在家目錄產生 ssh 公鑰和私鑰
  ```sh
  ssh-keygen
  ```

2. 進入家目錄，將公鑰 `.pub` 檔案裡面的文字複製到 github 上。


<br/>

<br/>

## 多個使用者
建立 config 檔案
```sh
touch ~/.ssh/config

vim ~/.ssh/config
```
config檔案內容
```sh
# Default GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

Host github-office # 取一個 alias
  HostName github.com # github.com / gitlab.com 二選一
  User git  # 若要使用 git 則統一使用這個名稱
  IdentityFile ~/.ssh/id_rsa_office # 私鑰檔案

Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_personal
```

做 github 設定時，只需改 Host 參數，User參數不影響
git@`{HOST}`:Liang60711/Note.git

```sh
git clone git@github-personal:<gh_username>/<gh_reponame>.git
```