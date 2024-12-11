---
Date: 2024-06-25
tags:
  - k8s
  - devops
---
Kubernetes 中的 Volume 基本上繼承了 Docker 中 Volume 的概念。
- 普通的volume只是為了一個Pod中的多個容器之間可以共享數據，它具有和pod相同的周期，所以本質上不具備持久化的功能
- Persistent Volume 是指能夠將資料進行持久化的一種資源對象，它可以獨立於 Pod 存在，週期與 Pod 無關，因此也決定了 PersistentVolume 不應該附著於任何一個存儲機節點，否則必然與 Pod調度產生干擾限制。
## emptyDir
當 Pod 被指派到節點時，會首先創建一個 emptyDir 卷，並且只要該 Pod 在該節點上運行，它就會存在。顧名思義，emptyDir 卷最初是空的。Pod 中的所有容器都可以讀寫 emptyDir 卷中的相同文件，儘管該卷可以在每個容器中掛載在相同或不同的路徑。當 Pod 因任何原因從節點中移除時，emptyDir 中的數據會被永久刪除。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: multicontainer-pod
spec:
  containers:
  - name: producer
    image: busybox
    command: ["sh", "-c", "while true; do echo $(hostname) $(date) >> /var/log/index.html; sleep 10; done"]
    volumeMounts:
    - name: webcontent
      mountPath: /var/log
  - name: consumer
    image: nginx
    ports:
      - containerPort: 80
    volumeMounts:
    - name: webcontent
      mountPath: /usr/share/nginx/html
  volumes:
  - name: webcontent
    emptyDir: {}
```
### Demo
我們使用 yml 檔案創建 pod 後，進入到了 pod 裡面查看是否有 index.yml 以及資料有沒有更新寫入進去。
![[K8s - Volumes 1.png]]

而 producer 的 pod 與 nginx 的 pod 也是在同一個網路下，所以我們可以使用 wget 來獲得 nginx 部屬的資料。
![[K8s - Volumes 2.png]]
## hostPath
hostPath 卷會將主節點文件系統中的文件或目錄掛載到您的 Pod 中。這不是大多數 Pod 需要的功能，但對於某些應用程序來說，它提供了一個強大的解決辦法。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: multicontainer-pod
spec:
  containers:
  - name: producer
    image: busybox
    command: ["sh", "-c", "while true; do echo $(hostname) $(date) >> /var/log/index.html; sleep 10; done"]
    volumeMounts:
    - name: webcontent
      mountPath: /var/log
  - name: consumer
    image: nginx
    ports:
      - containerPort: 80
    volumeMounts:
    - name: webcontent
      mountPath: /usr/share/nginx/html
  volumes:
  - name: webcontent
    hostPath:
      path: /tmp
      type: Directory
```
### Demo
我們使用 yml 檔案創建 pod，並且 apply 它，我們會發現它運行在 worker2 上面
![[K8s - Volumes 3.png]]

進入至 worker2 查看 `/tmp` 路徑底下的 `index.html`，確實有成功變更
![[K8s - Volumes 4.png]]
