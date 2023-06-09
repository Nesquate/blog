---
title: Git 遠端儲存庫強制蓋台
tags:
  - Git
  - 版本控制
categories:
  - - 程式撰寫
date: 2022-10-12 15:54:10
---

稍微記錄一下不然之後又忘記。

原本 `im-nex.one` 是用 Hugo 靜態網頁產生器去做首頁，後來部落格決定搬來這位置之後，就想說把原本的 Hugo 蓋掉。

但是我沒有先把儲存庫先 Clone 下來，所以有點麻煩。

一開始我天真浪漫想說，阿就 `git clone --no-checkout` 就好了，這也是[一開始找到的解法](https://stackoverflow.com/questions/38999901/clone-only-the-git-directory-of-a-git-repo)。

但是很快就遇到非空資料夾的問題 (Git 不允許在有資料的資料夾內 Clone Git 儲存庫)，所以後來參考了[另外一種解法](https://stackoverflow.com/questions/2411031/how-do-i-clone-into-a-non-empty-directory)。

自己的版本如下:

```shell
# 先初始化本地的 Git 儲存庫
git init

# 記得要蓋台分支名稱要跟遠端儲存庫上的一致，否則在 push 階段要手動指定
git branch -m main

# 新增遠端儲存庫
git remote add origin <remote_url>

# 然後把遠端儲存庫先抓下來，但不合併或 checkout
git fetch

## 然後就是做各種 commit 等操作，最後上傳的時候要加上 `-f` 參數
git push --set-upstream orign -f
```

這樣就強制蓋台成功了，不過強制蓋台意味著上面原本的資料也會不見，所以請先做好備份。