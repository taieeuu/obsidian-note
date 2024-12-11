---
Date: 2024-06-30
tags:
  - devops
  - git
---
## Hash 算法
- 是把任意長度的輸入通過**散列算法**變換成固定長度的輸出。
	- MD5 128bit
	- SHA1 160bit
	- SHA256 256bit
	- SHA512 512bit
## Blob
檢查檔案內容
```shell
cat hello.txt
#-----result-----
hello git
```

檔案資訊
```shell
tai@LAPTOP-OKJPH9JJ:/mnt/d/Project/Note/git/git-demo$ ls -lh
total 0
-rwxrwxrwx 1 tai tai 10 Jun 30 00:59 hello.txt
```
>[!info]
>10 byte

計算 Blob SHA1 散列值，與使用 `git add` 後的哈希一致。
```shell
tai@LAPTOP-OKJPH9JJ:/mnt/d/Project/Note/git/git-demo$ printf "blob 10\0hello git\n" | shasum
8d0e41234f24b6da002d962a26c2495ea16a425f  -
```
![[Pasted image 20240630012933.png]]
>[!info]
>這邊不使用 `echo` 指令而使用 `printf` ，`printf` 指令比 `echo` 更適合處理原始資料，因為 `printf` 可以精確控制輸出的格式，不會自動添加換行符號。這對於計算 Git Blob 的 SHA1 散列值尤為重要，因為任何多餘的字符（如換行符）都會改變輸出的散列值。

計算 Blob 的 SHA1 散列值
```shell
printf "blob 10\0hello git\n" | shasum
```
>[!info]
>- **Blob Header**
>	- `blob 10\0`: `blob` 是類型，`10` 是內容的字節數，`\0` 是分隔符。
>- **Blob 內容**
>	- `hello git\n`: 這是實際的檔案內容，包含一個換行符。
>- **計算 SHA1 散列值**
>	- 使用 `shasum` 來計算完整 Blob（header + 內容）的 SHA1 散列值。
### Git Cat-File 檢視 Blob 物件
1. 確認物件類型
```shell
git cat-file -t 8d0e
#-----result-----
blob
```

2. 顯示物件內容
```shell
git cat-file -p 8d0e
#-----result-----
hello git
```

3. 顯示物件大小
```shell
git cat-file -s 8d0e
#-----result-----
10
```


```shell
cat .git/objects/8d/0e41234f24b6da002d962a26c2495ea16a425f

# ----result-----
xK��OR04`�H���WH�,�6A�
```
>[!info]
>我們實際查看了 Blob 物件的檔案，發現內容是二進制編碼的。這是因為 Git 會對儲存的物件進行壓縮與編碼。

當然我們也可以反編譯回去查看裡面內容，使用 `zlib` 來解壓縮。