---
Date: 2024-07-14
tags:
  - git
  - devops
---
## Sync with upstream
### GUI
Github 中目前也有提供GUI模式做使用。
![[Git - Async Fork upstream 1.png]]
### 使用命令行同步
1. **配置upstream遠端**：
在同步 fork 之前，你必須配置一個指向上游倉庫的遠端。
```shell
git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git
```
 
2. **從upstream倉庫抓取更改**：
```shell
git fetch upstream
```
    
3. **檢出你的 fork 的本地分支**：
```shell
git checkout main
```    
    
4. **合併上游更改到本地分支**：
```shell
git merge upstream/main
```
    
5. **推送更改到 GitHub**：
同步你的本地倉庫後，將更改推送到 GitHub：
```shell
git push origin main
```
