---
data: 2023/12/10
tags:
  - devops
  - git
---
## 常用
### 創建
```cmd=
git branch -a #常看所有branch
git branch -r #查看遠端branch
git branch <branch_name> #創立name branch
```

```cmd=
git checkout -b branch_name 創建遠端跟本地branch
git checkout name 切換branch
```

```cmd=
git push -u origin name 推送origin遠端的name branch ,-u代表設定默認branch
git push origin <local_branch_name>:<different_remote_branch_name> 推送本地local_branch至origin遠端different_name的branch
```

### 拉取分支
1. 切換分支
```
git checkout <branch_name>
```
2. 拉取
```
git pull origin <branch_name>
```
### 查看目前分支
```
git branch
```

### 本地新增分支推至github/gitlab
```
git checkout <YourBranchName>

git push origin <YourBranchName>:<YourBranchName>
```
### 如gitlab/github上有新增資料，則需pull/fetch
```
```

如何在一台電腦使用多個Git帳號
---
https://medium.com/@hyWang/%E5%A6%82%E4%BD%95%E5%9C%A8%E4%B8%80%E5%8F%B0%E9%9B%BB%E8%85%A6%E4%BD%BF%E7%94%A8%E5%A4%9A%E5%80%8Bgit%E5%B8%B3%E8%99%9F-907c8eadbabf


---
## 詳細Git用法


### 查訊版本

#### **git log**

```shell
git log
```

### 切換分支

#### **git checkout** 

~~~shell
git checkout branch_name
# 切換到 branch-name
~~~

### 檢查遠程來源

#### **git remote**

~~~shell
git remote -v
~~~

### 創建分支

#### **git branch**

~~~shell
git branch
# 列出目前有多少 branch
git branch -r
# 列出所有 Repository branch 
git branch -a
# 列出所有 branch
git branch <new-branch>
# 產生新的 branch (名稱: new-branch)
git branch <new-branch> 'Any commit'
# 由 Any commit 產生新的 branch(new-branch)
git checkout -b <new-branch>
# 產生新的 branch, 並同時切換過去 new-branch
git branch -d <new-branch>
# 刪除 new-branch，需執行過 merge 的分支
git branch -D <new-branch>
# 強制刪除 new-branch
git branch -f <branch-name> <A>
# 強制移動 branch 指向 A
git branch -vv 
# 查看當前分支
git show-branch -a
#查看所有分支與其歷史提交紀錄(圖)
~~~

### 維護遠端檔案

#### **git remote**

~~~shell
git remote
#列出所有 remote
git remote add <A> <Web>
# 加入遠端 Repository 命名為 A
git remote rm <A>
# 移除 A
git remote update
# 更新所有 Repository branch
git remote set-url <A> <Web>
# 更改 A 的遠端位址
git ls-remote
# 列出遠端所有分支
~~~

### **查看內容**

#### **git show**

~~~shell
git show <A>
# 查 A 的內容
~~~

### 新增檔案

#### **git add**

~~~shell
git add .
# 全部加入(不含刪除檔案)
git add -u
# --update 只加入修改過的檔案，新增的檔案不加入
git add -A
# 全部加入 = git add . + git add -u
git add <fileA>
# 加入 fileA
~~~

### 抓取遠端 Repository

#### **git fetch**

~~~shell
git fetch
# 預設為 origin
git fetch <A>
# 抓取 A，但不影響 local
git fetch --tags
# 抓取 tags
git fetch --all
# 抓取所有 remotes
~~~

###  刪除檔案

#### **Git mv**

~~~shell
git mv <fileA> <fileB>
# 將 A 改為 B
~~~

### 提交

#### **Git Commit**

~~~shell
git commit
# 提交
git commit -m 'commit message'
# 快速提交
git commit -a
# 所有修改的檔案都 commit，但是 untracked file 還是得要先 add
git commit -v
# 加入比對結果
git commit --amend
# 修改前一次提交 
~~~

### 重置

#### **Git reset**

~~~shell
git reset --hard <commit>
# 還原到特定 commit，不保留檔案
git reset --soft <commit>
# 還原到特定 commit，保留檔案
git reset HEAD <A>
# A 從 staging area 狀態回到 untracked (檔案內容並不會改變)
~~~

### 下載遠端 

#### **git pull** 

~~~shell
git pull
# 預設為 origin
git pull <A>
# 抓取 A，直接更新 local
~~~

### 提交至遠端 

#### **git push**

~~~shell
git push
# 預設為 origin
git push <A>
# 提交 A
git push <A> --force
# 強制提交 A，可用在上傳錯誤，強制更正 GitHub
git push <A> <branchB>
# 指定分支 branchB 提交 A
git push --tags
# 提交 tags
git push --all
# 提交所有 branches
~~~

### 合併

#### **Git merge**

~~~shell
git merge <A> <B> <C> ...
# 將 A, B, C ... 合併進來
~~~

### 查看 commit 紀錄

##### **Git blame**

~~~shell
git blame <fileA>
# 關於 fileA的所有 commit 紀錄
~~~

### 接到其他分支

#### **Git rebase**

將 HEAD 之後所有 commit(含 HEAD) 接到其他 commit 上(接支)，若有重覆會忽略之 可能會導致無法上傳遠端

~~~shell
git rebase <branchA>
# 基礎版本設為 branchA，並執行本分支上的所有變更 (開頭接到 branchA)
# 目前在 C，執行 git rebase A，會從 a 到 C 接到 A 的最後面
    o---a---o---o---o---o---o---o---o  A
         \                           \
          o---o---o---o---o  B        o'---o'---o'---o'---o'
                                                           \
                                                            o'---o'---o'  *C
git rebase  [--onto <newbase>] [<upstream> [<branch>]]
# 找出 upstream 與 branch 的共同袓先，branch 由此之後的變化到終點這段，接在 newbase(新分支點) 上
# git rebase --onto A B C
    o---o---o---o---o---o---o---o---o  A
        |                            \
        |                             o'--o'--o'  *C
         \
          o---o---o---o---o  B
git rebase <commitA> -i
# 互動式編輯從目前 commit 到 commitA(不含) 的所有 commit
調換順序 => 直接調換即可
修正 commit 訊息 => reword or r
編輯，拆解，新增 commit => edit or e
與上一個 commit 合併，並合併訊息進行編輯 => squash
與上一個 commit 合併，但只保留上一個 commit 的訊息 => fixup
刪除 commit => 直接移除
~~~

## 基本設定

### git config

~~~shell
$ git config --global user.name "Your Name"
$ git config --global user.email you@example.com'
~~~

## Git SSH認證做法

https://tsengbatty.medium.com/git-%E8%B8%A9%E5%9D%91%E7%B4%80%E9%8C%84-%E4%BA%8C-git-clone-with-ssh-keys-%E6%88%96-https-%E8%A8%AD%E5%AE%9A%E6%AD%A5%E9%A9%9F-bdb721bd7cf2

## 文章

https://zwindr.blogspot.com/2016/01/git.html<br>
https://git-tutorial.readthedocs.io/zh/latest/configurations.html
