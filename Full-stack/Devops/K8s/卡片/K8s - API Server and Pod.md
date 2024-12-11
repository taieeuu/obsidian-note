---
Date: 2024-06-15
tags:
  - devops
  - k8s
---
## API Servere和API Object
### API Server
Kubernetes 的 API Server 是一個客戶端/伺服器的架構。
- 透過HTTP對外提供RESTful API服務，客戶端提交請求，伺服器回复
- 是無狀態的stateless，所有的狀態都儲存在叢集儲存中（etcd）
![[K8s - API Server and Pod 1.png]]

```shell
$ kubectl cluster-info
$ kubectl config view
$ kubectl config view --raw
$ kubectl config get-contexts
```
>[!info]
>- kubectl cluster-info: 查看集群信息
>- kubectl config view: 查看 `kubectl` 配置信息
>- kubectl config view --raw: 查看 `kubectl` 配置信息（原始格式）
>- kubectl config get-contexts: 取得`kubectl`配置中的所有上下文
### #### Clients
- kubectl
- RESTful API
- other clients
### API Object
API對像是透過API伺服器可以操作的Kubernetes對象，它們代表了整個叢集的狀態，例如：
- 正在運行哪些容器化應用程式（以及在哪些節點上）
- 這些應用程式可用的資源
- 圍繞這些應用程式行為方式的策略，例如重新啟動策略、升級和容錯

API物件透過以下欄位組織起來
- Kind (Pod, Deployment, Service, etc.)
- Group (core, apps, storage), see [https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/)
- Version (v1, beta, alpha) see [https://kubernetes.io/docs/reference/using-api/#api-versioning](https://kubernetes.io/docs/reference/using-api/#api-versioning)
### 如何操作API Object
兩種模式
- Imperative Configuration（直接透過命令列去配置，操作）
- Declarative Configuration（透過YAML/JSON格式定義Manifest，把期望狀態定義在檔案中，然後把檔案傳給API伺服器）

```yml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```

```shell
$ kubectl apply -f nginx.yml  # Declarative Configuration
$ 
# Imperative Configuration
```
## Pod 是什麼？
Pod是k8s裡最小的調度單位。
- 一組一個或多個應用程式容器及其共用資源（例如磁碟區）。
- Pod 共享與網路名稱空間相同的名稱空間（具有相同的 IP 位址。）
- Pod是K8s調度的最小單位

![[K8s - API Server and Pod 2.png|500]]
## 如何建立 Pod？
主要分為兩種: 1. 命令式 2. yml
#### 命令式
使用 nginx:latest 鏡像建立名為 web 的 pod
```shell
$ kubectl run web --image=nginx
```
#### yml
以下yaml檔案是定義一個pod所需的最少欄位（nginx.yml）
```yml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
   containers:
    - name: nginx-container
      image: nginx:latest
```

```shell
kubectl apply -f nginx.yml
```
##### Sample 
執行一個指令, sh -c “sleep 1000000”
```yml
apiVersion: v1
kind: Pod
metadata:
   name: client
spec:
   containers:
    - name: client
      image: busybox
      command:
       - sh
       - -c
       - "sleep 1000000"
```
### 多容器 Pod
一個pod是可以包含多個容器的，如果要建立這樣的pod，那麼只能透過yaml檔案實現，例如：
```yml
apiVersion: v1
kind: Pod
metadata:
   name: my-pod
spec:
   containers:
    - name: nginx
      image: nginx
    - name: client
      image: busybox
      command:
       - sh
       - -c
       - "sleep 1000000"
```
>[!info]
>- **apiVersion: v1**: 指定 Kubernetes API 的版本。`v1` 是常用的版本之一，適用於核心組件如 Pod
>- **kind: Pod**: 指定資源的類型是 Pod。Pod 是 Kubernetes 中最小的可部署單位，可以包含一個或多個容器。
>- **metadata**
>	- **name: my-pod**: 定義 Pod 的名稱為 `my-pod`。這個名稱在同一命名空間內必須是唯一的。
>- **spec**: 定義 Pod 的具體規範，包括容器及其配置。
>- **containers**: 定義了 Pod 中的兩個容器。
>	1. **nginx 容器**
>		- **name: nginx** 
>			- 定義容器的名稱為 `nginx`。
>		- **image: nginx** 
>			- 指定容器使用的映像為 `nginx`。這會從 Docker Hub 拉取 `nginx` 映像並運行。
>	1. **client 容器**
>		- **name: client**
>			- 定義容器的名稱為 `client`。
>		- **image: busybox**
>			- 指定容器使用的映像為 `busybox`。`busybox` 是一個小型的 Linux 發行版，常用於簡單的系統管理任務。
>		- **command**
>			- 指定容器運行時執行的命令。
>		- **- sh**
>			- 使用 `sh` shell。
>		- **- -c**
>			- 指定要執行的命令參數。
>		- **- "sleep 1000000"**
>			- 指定容器執行的命令是 `sleep 1000000`，這會讓容器進入休眠狀態，持續約 11.5 天。這種設置常用於保持容器長時間運行而不退出。

```shell
$ kubectl create -f my-pod.yml
$ kubectl get pod
NAME     READY   STATUS    RESTARTS   AGE
my-pod   2/2     Running   0          35s
```
### Pod YAML 語法
查詢文件 kubernetes.io，命令列幫助
```shell
kubectl explain pods | more
kubectl explain pod.spec | more
kubectl explain pod.spec.containers | more
```

## Kubectl 參數 --dry-run
`--dry-run` 參數在 Kubernetes 的 `kubectl` 指令中用來模擬執行操作，而不會實際應用或改變集群中的資源。這對於驗證配置文件的正確性和操作的可行性非常有用。

`--dry-run` 參數有兩種模式：`client` 和 `server`。
### Server-side
- **作用**：將請求發送到 API 伺服器進行驗證，模擬操作但不會持久化任何改變。
- **用例**：用於確保在伺服器端能夠成功執行操作且語法正確，不會實際改變任何資源。
```shell
kubectl apply -f nginx.yml --dry-run=server
```
>[!info]
>- **`kubectl apply`**：這個命令用來應用配置文件中的資源到 Kubernetes 集群。
>- **`-f nginx.yml`**：指定要應用的配置文件 `nginx.yml`。
>- **`--dry-run=server`**：模擬操作而不實際執行，將請求發送到 API 伺服器進行驗證，但不會持久化改變。
### Client-side
- **作用**：模擬操作並將結果輸出到標準輸出（stdout），不會和 API 伺服器進行任何交互。
- **用例**：用於驗證本地配置文件的語法是否正確，或者生成符合語法的 YAML 配置文件。
```shell
$ kubectl apply -f nginx.yml --dry-run=client
$ kubectl run web --image=nginx --dry-run=client -o yaml
$ kubectl run web --image=nginx --dry-run=client -o yaml > nginx.yml
```
>[!info]
>- **kubectl run web**
>	- `kubectl run` 是用來創建一個新的 Pod 或 Deployment 的命令。
>	- `web` 是這個 Pod 或 Deployment 的名稱。
>- **--image=nginx**  
>	- 指定這個 Pod 使用的 Docker 映像為 `nginx`。
>- **--dry-run=client**
>	- 這個參數表示模擬這個操作，但不實際創建任何資源。
>	- `client` 模式表示這個檢查是在本地進行的，而不是發送到伺服器檢查。
>- **-o yaml**  
>	- 指定輸出格式為 YAML。這會生成相應的 YAML 配置文件內容。
>- **> nginx.yml**  
>	- 將輸出的 YAML 內容重定向到一個名為 `nginx.yml` 的文件中。這意味著生成的 YAML 配置文件會被保存到當前目錄中的 `nginx.yml` 文件。
## kubectl 參數 diff
顯示目前要部署的manifest和叢集中運行的有和不同，這樣就知道如果apply會發生什麼。
```shell
kubectl diff -f new-nginx.yml
```
## Pod 的基本操作
### 創建pod
有兩種方式 1. `kubectl apply -f my-pod.yml` 2. `kubectl run`

在 Kubernetes 集群中創建一個新的 Pod，該 Pod 會運行一個名為 `web2` 的容器，並使用 `nginx` Docker 映像。
```shell
tai@tai-virtual-machine:~$ kubectl run web1 --image=nginx
pod/web1 created
tai@tai-virtual-machine:~$ kubectl run web2 --image=nginx
pod/web2 created
```
>[!info]
>- **`kubectl run`**：這個命令用來創建和運行一個新的 Pod。
>- **`web2`**：這是你創建的 Pod 的名稱。在 Kubernetes 集群中，這個名稱必須是唯一的。
>- **`--image=nginx`**：指定使用 `nginx` Docker 映像來運行容器。`nginx` 是一個流行的 HTTP 和反向代理服務器映像。

使用 YAML 配置文件和 `kubectl apply` 創建 Pod
```shell
kubectl apply -f my-pod.yml
```
>[!info]
>如果要驗證有無正確的話可加上`--dry-run`
>
>- **kubectl apply**: 用於根據 YAML 或 JSON 配置文件中的描述創建或更新資源。這個命令可以應用到各種 Kubernetes 資源，如 Pod、Deployment、Service 等。
### 基本語法
### 取得pod清單
```shell
tai@tai-virtual-machine:~$ kubectl get pods
NAME   READY   STATUS    RESTARTS   AGE
web1   1/1     Running   0          2m
web2   1/1     Running   0          115s
tai@tai-virtual-machine:~$ kubectl get pods -o wide
NAME   READY   STATUS    RESTARTS   AGE    IP           NODE          NOMINATED NODE   READINESS GATES
web1   1/1     Running   0          2m5s   10.244.2.3   k8s-worker2   <none>           <none>
web2   1/1     Running   0          2m     10.244.1.3   k8s-worker1   <none>           <none>
```
>[!info]
>- **`**-o wide`**: 列出詳細的訊息，後面也可加上`--show-labels`列出label等的訊息，等等的操作。

透過可以取得到具體一個pod的yaml定義文件`-o yaml`
```shell
kubectl get pods client -o yaml
```
### 刪除Pod
```shell 
$ kubectl delete pod web
pod "web" deleted
```
>[!info]
>- **`delete`**: 這個通用命令可以刪除幾乎任何k8s API Object、Resource。
>
>**語法**:
>- kubectl delete <resource_type> <resource_name>
### 取得pod詳細資料
```shell
$ kubectl describe pod my-pod
```
## 進入容器執行指令
對於只有單一容器的Pod，執行date指令
```shell
tai@tai-virtual-machine:~$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
client   1/1     Running   0          47m
web      2/2     Running   0          2m43s
tai@tai-virtual-machine:~$ kubectl exec client -- date
Sun Jun 16 04:31:38 UTC 2024
```
>[!info]
>這條命令會進入名為 `client` 的 Pod，並在該 Pod 中的主容器內執行 `date` 命令。執行結果會顯示在你的終端中。
>
>參數解說:
>- **`kubectl exec`**：
>	- `kubectl exec` 是 Kubernetes 中用來在 Pod 中執行命令的命令。
>- **`client`**：  
>	- 指定要在其內部執行命令的 Pod 名稱。在這個例子中，Pod 的名稱是 `client`。
>- **`--`**：
>	- 分隔符，用來將 `kubectl exec` 命令的選項與要在容器內部執行的命令分隔開。
>- **`date`**：  
>	- 要在 Pod 中執行的命令。在這個例子中，是 `date` 命令，用來顯示當前日期和時間。

進入互動式shell
```shell
tai@tai-virtual-machine:~$ kubectl exec client -it -- sh
/ #
```

對於具有多個容器的pod，需要透過`-c`指定要進入該容器。
```shell
tai@tai-virtual-machine:~$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
client   1/1     Running   0          55m
web      2/2     Running   0          10m
tai@tai-virtual-machine:~$ kubectl exec web -- date
Defaulted container "web" out of: web, busybox
Sun Jun 16 04:39:40 UTC 2024
tai@tai-virtual-machine:~$ kubectl exec web -c busybox -- date
Sun Jun 16 04:39:58 UTC 2024
```
>[!info]
>可以看到如果沒有指定一樣會有預設第一個 container 執行該命令

## kubectl API级别log顯示
透過`-v`可以取得到API等級的每個kubectl指令的日誌

例如: 取得kubectl操作更多的日誌、API等級（透過詳細 -v 指定）
```shell
$ kubectl get pod <pod-name> -v 6   # 或者 7,8,9 不同的level，数值越大，得到的信息越详细
```

`--watch`持續監聽kubectl操作，API級別
```shell
$ kubectl get pods <pod-name> --watch -v 6
```
## kubectl proxy
一種直接存取kubernetes API的方法。

先透過kubectl取得到API的URL，例如
```shell
tai@tai-virtual-machine:~$ kubectl get namespaces  -v 6
I0616 13:06:15.059699   71766 loader.go:395] Config loaded from file:  /home/tai/.kube/config
I0616 13:06:15.069815   71766 round_trippers.go:553] GET https://192.168.118.129:6443/api/v1/namespaces?limit=500 200 OK in 6 milliseconds
NAME              STATUS   AGE
default           Active   27h
kube-flannel      Active   17h
kube-node-lease   Active   27h
kube-public       Active   27h
kube-system       Active   27h
```

後台服務`kubectl proxy &`
```shell
tai@tai-virtual-machine:~$ kubectl proxy &
[1] 71871
tai@tai-virtual-machine:~$ Starting to serve on 127.0.0.1:8001

tai@tai-virtual-machine:~$
```

這個時候就可以透過代理來存取API了，例如
```shell
tai@tai-virtual-machine:~$ curl http://127.0.0.1:8001/api/v1/namespaces?limit=500
{
  "kind": "NamespaceList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "140471"
  },
  "items": [
    {
      "metadata": {
        "name": "default",
        "uid": "872ed8f9-6c24-4105-ada1-eb940b217991",
        "resourceVersion": "42",
        "creationTimestamp": "2024-06-15T01:50:25Z",
        "labels": {
          "kubernetes.io/metadata.name": "default"
...
```
### 退出proxy？
```shell
tai@tai-virtual-machine:~$ fg
kubectl proxy
^C
tai@tai-virtual-machine:~$
```
## Namespace
### Basic
獲取全部的 namespace
```shell
tai@tai-virtual-machine:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   27h
kube-flannel      Active   18h
kube-node-lease   Active   27h
kube-public       Active   27h
kube-system       Active   27h
```

取得所有 api 資源的清單以及它們是否可以/不可以命名空間
```shell
kubectl api-resources --namespaced=true | head
kubectl api-resources --namespaced=false | head
```

查看 namespace 詳細資訊
```shell
kubectl describe namespaces <namespace-name>
```

獲取 Kubernetes 集群中所有命名空間（Namespace）內的所有 Pod
```shell
kubectl get all --all-namespaces

or

kubectl get all -A
---
NAMESPACE      NAME                                          READY   STATUS    RESTARTS        AGE
default        client                                        1/1     Running   0               128m
default        web                                           2/2     Running   0               83m
kube-flannel   kube-flannel-ds-flb5z                         1/1     Running   1 (5h21m ago)   17h
kube-flannel   kube-flannel-ds-lgfh4                         1/1     Running   1 (5h20m ago)   18h
kube-flannel   kube-flannel-ds-pbk2k                         1/1     Running   1 (5h20m ago)   17h
kube-system    coredns-76f75df574-45xrd                      1/1     Running   1 (5h20m ago)   28h
kube-system    coredns-76f75df574-p86l4                      1/1     Running   1 (5h20m ago)   28h
kube-system    etcd-tai-virtual-machine                      1/1     Running   1 (5h20m ago)   28h
kube-system    kube-apiserver-tai-virtual-machine            1/1     Running   1 (5h20m ago)   28h
kube-system    kube-controller-manager-tai-virtual-machine   1/1     Running   1 (5h20m ago)   28h
kube-system    kube-proxy-mpm6b                              1/1     Running   1 (5h20m ago)   17h
kube-system    kube-proxy-nbbss                              1/1     Running   1 (5h21m ago)   17h
kube-system    kube-proxy-sp86l                              1/1     Running   1 (5h20m ago)   28h
kube-system    kube-scheduler-tai-virtual-machine            1/1     Running   1 (5h20m ago)   28h
```

獲取 Kubernetes 集群中所有命名空間內的所有資源的命令。這個命令會顯示多種類型的 Kubernetes 資源，包括 Pod、Service、Deployment、ReplicaSet 等。
```shell
kubectl get all --all-namespaces
```

創建 namespaces (使用命令)
```shell
kubectl create namespace <namespaces-name>

kubectl create namespace Playground1   # will be error
```

創建 namespaces (使用 yml ) ，如果忘記如何寫 yml 可使用第一個命令生成 yml
```shell
tai@tai-virtual-machine:~$ kubectl create namespace demo --dry-run=client -o yaml > demo.yaml
tai@tai-virtual-machine:~$ more demo.yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: demo
spec: {}
status: {}
tai@tai-virtual-machine:~$ kubectl apply -f demo.yaml
namespace/demo created
```

刪除 namespaces
```shell
tai@tai-virtual-machine:~$ kubectl delete namespaces demo
namespace "demo" deleted
```
### Pod with namespace
獲取所有在 namespaces 中的所有 pod
```shell
tai@tai-virtual-machine:~$ kubectl get pod -A
NAMESPACE      NAME                                          READY   STATUS    RESTARTS        AGE
default        client                                        1/1     Running   0               137m
default        web                                           2/2     Running   0               92m
kube-flannel   kube-flannel-ds-flb5z                         1/1     Running   1 (5h30m ago)   17h
kube-flannel   kube-flannel-ds-lgfh4                         1/1     Running   1 (5h30m ago)   18h
kube-flannel   kube-flannel-ds-pbk2k                         1/1     Running   1 (5h30m ago)   17h
kube-system    coredns-76f75df574-45xrd                      1/1     Running   1 (5h30m ago)   28h
kube-system    coredns-76f75df574-p86l4                      1/1     Running   1 (5h30m ago)   28h
kube-system    etcd-tai-virtual-machine                      1/1     Running   1 (5h30m ago)   28h
kube-system    kube-apiserver-tai-virtual-machine            1/1     Running   1 (5h30m ago)   28h
kube-system    kube-controller-manager-tai-virtual-machine   1/1     Running   1 (5h30m ago)   28h
kube-system    kube-proxy-mpm6b                              1/1     Running   1 (5h30m ago)   17h
kube-system    kube-proxy-nbbss                              1/1     Running   1 (5h30m ago)   17h
kube-system    kube-proxy-sp86l                              1/1     Running   1 (5h30m ago)   28h
kube-system    kube-scheduler-tai-virtual-machine            1/1     Running   1 (5h30m ago)   28h
```

在預設命名空間中取得 pod
```shell
tai@tai-virtual-machine:~$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
client   1/1     Running   0          139m
web      2/2     Running   0          94m
```

獲取在 demo namespace 中的 pod
```shell
kubectl get pods --namespace=demo
```

在 demo 的命名空間中建立一個 pod
```shell
kubectl run web --namespace=demo --image=nginx
pod/web created

# or with yaml file
more web.yml

apiVersion: v1
kind: Pod
metadata:
  name: web
  namespace: demo
spec:
  containers:
  - image: nginx
    name: web
    
kubectl apple -f web.yml
```
### Change default namespace
如何切換目前預設的命名空間從default到demo

顯示當前 Kubernetes 配置文件（通常是 `~/.kube/config`）中所有上下文（context）的命令。上下文包含了與 Kubernetes 集群交互所需的信息，如集群、用戶和命名空間等。
```shell
tai@tai-virtual-machine:~$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin
```

修改當前上下文的默認命名空間。這樣做的目的是讓後續的 `kubectl` 命令在指定的上下文中默認使用新的命名空間，而無需每次都指定 `--namespace` 選項。
```shell
tai@tai-virtual-machine:~$ kubectl config set-context --current --namespace demo
Context "kubernetes-admin@kubernetes" modified.
```
>[!info]
>- **`kubectl config set-context`**：
>	- `kubectl config` 是用來管理 Kubernetes 配置文件的命令。
>	- `set-context` 是用來修改現有上下文或創建新上下文的子命令。
>- **`--current`**：
>	- 表示修改當前活動的上下文，而不是指定一個特定的上下文名稱。
>- **`--namespace demo`**：
>	- 設置當前上下文的默認命名空間為 `demo`。這意味著在當前上下文中，所有後續的 `kubectl` 命令都將默認作用於 `demo` 命名空間。

```shell
tai@tai-virtual-machine:~$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   demo
```
>[!info]
>再次查看設定，會發現預設 namespace 已改為 demo。
>

再次確認，直接輸入 `kubectl get pods` 不用再次指定 namespaces 。
```shell
tai@tai-virtual-machine:~$  kubectl get pods
NAME   READY   STATUS    RESTARTS   AGE
web    1/1     Running   0          4m14s
```
## Static Pod
### 甚麼是 Static Pods ?
Static Pods 是一種特殊類型的 Pod，它們由每個 Kubernetes 節點上的 `kubelet` 進程直接管理，而不是通過 Kubernetes API 伺服器管理。這些 Pods 通常用於運行 Kubernetes 控制平面的核心組件，如 API Server、etcd 和控制器管理器。
- 由 Node 上的 kubelet 管理
- 靜態 Pod 清單，kubelet 設定中的 staticPodPath，預設為 /etc/kubernetes/manifests
- kubelet 設定檔：/var/lib/kubelet/config.yaml
- pod可以透過API伺服器“看到”，但不能被API伺服器管理

```shell
tai@tai-virtual-machine:~$ kubectl get pods -A -o wide
NAMESPACE      NAME                                          READY   STATUS    RESTARTS        AGE     IP                NODE                  NOMINATED NODE   READINESS GATES
default        client                                        1/1     Running   0               3h10m   10.244.2.4        k8s-worker2           <none>           <none>
default        web                                           2/2     Running   0               145m    10.244.1.4        k8s-worker1           <none>           <none>
demo           web                                           1/1     Running   0               49m     10.244.2.5        k8s-worker2           <none>           <none>
kube-flannel   kube-flannel-ds-flb5z                         1/1     Running   1 (6h23m ago)   18h     192.168.118.130   k8s-worker2           <none>           <none>
kube-flannel   kube-flannel-ds-lgfh4                         1/1     Running   1 (6h23m ago)   19h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-flannel   kube-flannel-ds-pbk2k                         1/1     Running   1 (6h23m ago)   18h     192.168.118.128   k8s-worker1           <none>           <none>
kube-system    coredns-76f75df574-45xrd                      1/1     Running   1 (6h23m ago)   29h     10.244.0.5        tai-virtual-machine   <none>           <none>
kube-system    coredns-76f75df574-p86l4                      1/1     Running   1 (6h23m ago)   29h     10.244.0.4        tai-virtual-machine   <none>           <none>
kube-system    etcd-tai-virtual-machine                      1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-apiserver-tai-virtual-machine            1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-controller-manager-tai-virtual-machine   1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-proxy-mpm6b                              1/1     Running   1 (6h23m ago)   18h     192.168.118.128   k8s-worker1           <none>           <none>
kube-system    kube-proxy-nbbss                              1/1     Running   1 (6h23m ago)   18h     192.168.118.130   k8s-worker2           <none>           <none>
kube-system    kube-proxy-sp86l                              1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-scheduler-tai-virtual-machine            1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
```
>[!info]
>我們可以看到有些 pod 只有在 master 上面所運行的，那這些 pod 是一個 static pod。

### 如何查看 pod 是否為 static pod
確定一個 Pod 是否為 Static Pod 主要有兩種方式：
1. 使用 `kubectl describe` 命令查看 Pod 的詳細信息。
2. 檢查 `kubelet` 監控的 Static Pod 配置目錄。

#### 命令查看Pod 的詳細信息
`kubectl describe` 命令來查看 Pod 的詳細信息，並查找特定的標誌或字段來確定 Pod 是否為 Static Pod
```shell
tai@tai-virtual-machine:~$ kubectl get pods -A -o wide
NAMESPACE      NAME                                          READY   STATUS    RESTARTS        AGE     IP                NODE                  NOMINATED NODE   READINESS GATES
default        client                                        1/1     Running   0               3h10m   10.244.2.4        k8s-worker2           <none>           <none>
default        web                                           2/2     Running   0               145m    10.244.1.4        k8s-worker1           <none>           <none>
demo           web                                           1/1     Running   0               49m     10.244.2.5        k8s-worker2           <none>           <none>
kube-flannel   kube-flannel-ds-flb5z                         1/1     Running   1 (6h23m ago)   18h     192.168.118.130   k8s-worker2           <none>           <none>
kube-flannel   kube-flannel-ds-lgfh4                         1/1     Running   1 (6h23m ago)   19h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-flannel   kube-flannel-ds-pbk2k                         1/1     Running   1 (6h23m ago)   18h     192.168.118.128   k8s-worker1           <none>           <none>
kube-system    coredns-76f75df574-45xrd                      1/1     Running   1 (6h23m ago)   29h     10.244.0.5        tai-virtual-machine   <none>           <none>
kube-system    coredns-76f75df574-p86l4                      1/1     Running   1 (6h23m ago)   29h     10.244.0.4        tai-virtual-machine   <none>           <none>
kube-system    etcd-tai-virtual-machine                      1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-apiserver-tai-virtual-machine            1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-controller-manager-tai-virtual-machine   1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-proxy-mpm6b                              1/1     Running   1 (6h23m ago)   18h     192.168.118.128   k8s-worker1           <none>           <none>
kube-system    kube-proxy-nbbss                              1/1     Running   1 (6h23m ago)   18h     192.168.118.130   k8s-worker2           <none>           <none>
kube-system    kube-proxy-sp86l                              1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
kube-system    kube-scheduler-tai-virtual-machine            1/1     Running   1 (6h23m ago)   29h     192.168.118.129   tai-virtual-machine   <none>           <none>
tai@tai-virtual-machine:~$ kubectl describe pod --namespace kube-system etcd-tai-virtual-machine
...
```
>[!info]
> 從`kubectl describe pod` 命令輸出的信息來看，`etcd-tai-virtual-machine` Pod 很可能是一個 Static Pod。
> 
> **確認 Static Pod 的關鍵信息**
> 1. **Controlled By: Node/tai-virtual-machine**
> 	- 這個字段表明該 Pod 由節點 `tai-virtual-machine` 管理，而不是通過 Kubernetes 控制平面 API 伺服器管理的。這是 Static Pod 的一個特徵。
> 2. **Annotations 中的 `kubernetes.io/config.source: file`**
> 	- 這個註釋表明該 Pod 的配置源自一個文件，這是 Static Pod 的另一個明確指標。
#### 檢查 Static Pod 配置目錄
最終確認該 Pod 是否為 Static Pod，可以檢查 `kubelet` 監控的 Static Pod 配置目錄 `/etc/kubernetes/manifests`。如果這個目錄中有 `etcd-tai-virtual-machine` 的配置文件，那麼該 Pod 就是 Static Pod。
```shell
tai@tai-virtual-machine:~$ sudo ls /etc/kubernetes/manifests/
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```
>[!info]
>這些都是 Static Pod

如果我們想要創建自己的 static pod ，我們可以在這個**集群的任意一個節點創建**。在該節點的 `kubelet` 監控的配置目錄中創建一個 Pod 的 YAML 配置文件，這邊的監控的配置目錄也就是`/etc/kubernetes/manifests`。

### 創建自己的 static pod
對照組，通過`kubectl run`創建一個 nginx pod
```shell
kubectl run web1 --image nginx
```

比較組，因為我們要在文件在監控目錄中添加 yml 文件，所以先生成 yml 文件。
```shell
kebuectl run web2 --image nginx --dry-run=client -o yaml
```

在 k8s-worker1 中的 `kubectl` 創建 yml
```shell
sudo vim /etc/kubernetes/manifests/web2.yml
```
>[!info]
>從上述生成的 yml 文件複製貼上。

ps. 如有錯誤可以查看log
```shell
kubectl describe pod <pod-name>
```

創建完後，就會生成 `static pod`，可使用`kubectl get pods`查看
```shell
tai2@k8s-worker2:~$ kubectl get pods
NAME               READY   STATUS    RESTARTS      AGE
client             1/1     Running   1 (30m ago)   3d8h
web                2/2     Running   2 (30m ago)   3d7h
web2-k8s-worker2   1/1     Running   1 (30m ago)   2d21h
```

**重點**
`static pod` 是由 Kubelet 在每個節點上直接管理的，所以無法透過`kubectl api server`去刪除也就是像 `kubectl delete pod` 這些指令去刪除的。這是因為 `Static Pods` 是由 Kubelet 根據節點上的靜態配置文件來創建和管理的，而不是通過 Kubernetes 的控制平面（API Server）來管理的，只能透過刪除該 yml 檔案。

由 `kubelet` 直接管理的，不需要通過 `kubectl` 命令來創建或指定命名空間。因此，這些 Pod 默認會被創建在 `default` 命名空間中，因為靜態 Pod 的配置文件沒有包含命名空間的信息。

我們也可以通過當前系統中所有正在運行的進程，並過濾出包含 `kubelet` 的進程。來找出`kubelet`的啟動的設定檔案
```shell
tai2@k8s-worker2:~$ ps -ef | grep kubelet
root         981       1  1 19:55 ?        00:00:28 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.9
tai2       14798    7180  0 20:40 pts/1    00:00:00 grep --color=auto kubelet
```
>[!info]
>- `ps -ef`：列出當前系統中的所有進程。
>- `grep kubelet`：過濾出包含 `kubelet` 的行。
## Init Containers
- 初始化
- 處理依賴，控制啟動

測試範例

我們在當前 `~` 目錄創建 init.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-init-containers
spec:
  initContainers:
  - name: init-service
    image: busybox
    command: ["sh", "-c", "echo waiting for sercice; sleep 4"]
  - name: init-database
    image: busybox
    command: ["sh", "-c", "echo waiting for database; sleep 4"]
  containers:
  - name: app-container
    image: nginx
```

啟動
```shell
kubectl apply -f init.yml
```

監聽 pod 的變化
```shell
tai@tai-virtual-machine:~$ kubectl get pod --watch
NAME                       READY   STATUS     RESTARTS      AGE
pod-with-init-containers   0/1     Init:0/2   0             4s
web                        1/1     Running    1 (87m ago)   3d7h
pod-with-init-containers   0/1     Init:1/2   0             7s
pod-with-init-containers   0/1     Init:1/2   0             10s
pod-with-init-containers   0/1     PodInitializing   0             14s
pod-with-init-containers   1/1     Running           0             16s
```
## Pod 生命週期
**Linux補充知識**
Q: SIGTERM 與 SIGKILL：有什麼不同？

`SIGTERM` 和 `SIGKILL` 是兩種不同的信號，用於控制進程的終止，主要區別在於其信號的處理方式:
**SIGTERM**: 
- **用途**：用於請求進程正常終止。
- **行為**：允許進程執行清理操作，比如釋放資源、保存狀態、關閉文件等，並根據信號處理器的定義執行相應的代碼。
- **特點**：進程可以捕獲 `SIGTERM` 信號，進行自定義的清理操作，並返回適當的退出狀態。
- **終止方式**：進程在接收到 `SIGTERM` 信號後，會有一段時間來進行清理和退出，這個過程是可控的。

**SIGKILL**
- **用途**：強制終止進程，無法被捕獲或忽略。
- **行為**：立即終止進程，並且不允許進程執行任何清理操作或調用信號處理器。
- **特點**：一旦發送 `SIGKILL` 信號，進程立即被終止，無法阻擋或捕獲，無法執行任何清理代碼。
- **終止方式**：這是一個非可逆的操作，進程會被強制終止。

![[K8s - API Server and Pod 3.png]]
>[!info]
>- **Pending（等待中）**：
>	- 當 Pod 被創建後，處於 Pending 狀態，直到其容器開始運行。
>	- 此狀態表示 Pod 已經被接受，但其容器還沒有全部啟動。
>- **Running（運行中）**：  
>	- 當 Pod 中至少有一個容器正在運行時，Pod 進入 Running 狀態。
>	- 這表示 Pod 已經分配了必要的資源（如 CPU 和內存），並且其容器正在運行中。
>- **Succeeded（成功）**：
>	- 當 Pod 中所有容器都成功終止時，Pod 進入 Succeeded 狀態。
>	- 這表示 Pod 中的所有工作都已經完成，且沒有遇到錯誤。
>- **Failed（失敗）**：
>	- 當 Pod 中的至少一個容器異常終止時，Pod 進入 Failed 狀態。
>	- 這表示 Pod 未能成功完成其預定的工作，並且其中一些容器在運行過程中遇到錯誤並終止。
>- **Unknown（未知）**：
>	- 當 Kubelet 無法向 API Server 報告 Pod 的狀態時，Pod 進入 Unknown 狀態。
>	- 這可能是由於與該節點的通信中斷或其他問題，導致無法獲取 Pod 的最新狀態。
### 容器狀態
- 等待
- 跑步
- 終止
    - 進程終止/崩潰
    - Pod 已刪除
    - 節點故障或維護
    - 因缺乏資源而被驅逐
### 停止/終止 Pod
```shell
kubectl delete pod <name>
```
>[!info]
>執行刪除的時候，
>- API server會設定一個定時器（grace period Timer），預設是30秒
>- 同時 pod 狀態改成 Termination
>- Pod所在節點上的kubelet收到指令，會給Pod裡的容器發送SIGTERM訊號，然後容器退出等等
>- 如果容器在定時器到時退出了，那麼pod資訊同時會被API伺服器從儲存中刪除
>- 如果容器沒有在定時器到時退出，則 kubelet 之前會發送 SIGKILL 資訊到 pod 裡的容器，強制殺死容器，最後 API 伺服器更新儲存 etcd

寬限期計時器是可以修改的
```shell
$ kubectl delete pod <name> --grace-period=<seconds>
```

yml 的配置
```yml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  terminationGracePeriodSeconds: 10
  containers:
  - image: nginx
    name: web
```

或者也可以直接就強制刪除pod（SIGKILL）
```shell
$ kubectl delete pod <name> --grace-period=0 --force
```
### Pod 持久化
Pod 本身不會重新自動部署
如果一個pod停止了，它不會重新啟動，只能建立一個新的
如果有配置需要持久化怎麼辦？
- Pod 清單、秘密和 ConfigMap
- 環境變數

數據持久
- PersistentVolume
- PersistentVolumeClaim

### Container Restart Policy
Pod 的規範有一個 restartPolicy 字段，其可能值為`Always`、`OnFailure`和`Never`。預設值是`Always`。
```yml
vagrant@k8s-master:~$ kubectl run web --image nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: web
  name: web
spec:
  containers:
  - image: nginx
    name: web
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```