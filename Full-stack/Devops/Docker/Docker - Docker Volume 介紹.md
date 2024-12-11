---
Date: 2024-06-09
tags:
  - docker
  - devops
---
預設情況下，在運行中的容器裡建立的文件，被保存在一個可寫入的容器層：
- 如果容器被刪除了，則資料也沒有了
- 這個可寫入的容器層是和特定的容器綁定的，也就是這些資料無法方便的和其它容器共享
    
Docker主要提供了兩種方式做資料的持久化
- Data Volume, 由Docker管理，(/var/lib/docker/volumes/ Linux), 持久化資料的最佳方式
- Bind Mount，由使用者指定儲存的資料具體mount在系統什麼位置
但其實還有一種 tmpfs mount ，因不常使用，所以這邊不介紹
![[Pasted image 20240609011431.png]]


