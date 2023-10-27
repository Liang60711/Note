# 一般指令
查看紀錄
```
$ git log
```
查看詳細紀錄
```
$ git reflog
```

發佈至遠端指定的分支（Branch）
```
$ git push origin <BRANCH_NAME>
```

# git commit
可以將之前有 add 過的檔案一起 commit，略過 add
```
$ git commit -am 'message'
```


# branch
查看分支
```
$ git branch
```

建立分支
```
$ git branch <BRANCH_NAME>
```
前往分支
```
$ git checkout <BRANCH_NAME>
```
建立並前往分支
```
$ git checkout -b <BRANCH_NAME>
```
強制刪除分支
```
$ git branch -D <BRANCH_NAME>
```
修改分支名稱
```
$ git branch -m <OLD_BRANCH_NAME> <NEW_BRANCH_NAME>
```

# merge
main 合併(merge) feature
```
$ git checkout main
$ git merge feature
```


# rebase
main 合併(rebase) feature，main 分支會接在 feature 之後 
```
$ git checkout main
$ git rebase feature
```


# reset  
一般 reset，每個 ^ 為前1次(^^ 為前兩次或是 ~2)
```
# 等價
$ git reset <BRANCH_NAME>^^
$ git reset <BRANCH_NAME>~2
```
查詢詳細 SHA-1 值
```
$ git reflog
```
merge 或 rebase 的 reset (不能用 git reset HEAD^，分支不會復原)
```
$ git reflog
$ git reset --hard <HASH>
```

# revert
刪除 commit，但會留下一個刪除的 commit 紀錄，因為團隊中有可能不能使用 reset。

與 reset 不同的是，reset 刪除 commit 不會再留下一筆 commit 紀錄。

<br/>

<br/>

# 清除快取
```
$ git rm -r --cached .
```

<br/>

<br/>

# 多個 commit 壓縮成一個 commit
為了防止 commit 的顆粒度太細，不好 PR，使用 `squash` 將多個 commit 做成一個。

* 使用 Sourcetree:

    1. 對分支點右鍵 >> `Rebase children of xxxxxxx interactively` (使用互動式介面)。
    2. 點選 `squash with previous`，會顯示要壓縮的結果。
    3. 點選 `OK`。


<br/>

<br/>

# fast-forward 模式

快轉模式是使用在 merge 的時候

* 若不使用 fast-forward : 在 merge 時，會多出一個 commit，此 commit 表示 `將兩個分支合併`。
* 若使用 fast-forward : 在 merge 時，就不會額外產生 commit，可保持紀錄乾淨。
* 有時會遇到在本地端 commit 後，無法 pull 遠端的 commit，原因就是因為此 Repository 沒有開啟 fast-forward 模式，所以需要手動 merge，也就是 `先 fetch 再 merge`。
* 可以使用 `--fast-only` 或是 `--no-ff` 強制使用或不使用此模式。

> 舉例可參考 https://gitbook.tw/chapters/branch/merge-commit