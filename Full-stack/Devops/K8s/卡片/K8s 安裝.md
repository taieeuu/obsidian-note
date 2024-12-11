---
Date: 2024-06-15
tags:
  - k8s
  - devops
---
## 硬體
## kubeadm本地搭建3節點集群
### 關閉 Swap 功能
```shell
sudo swapoff -a
```

檢查是否成功關閉，若執行下面指令後沒有任何輸出，則代表關閉成功
```shell
sudo swapon --show
```
>[!info]
>- swapoff 的效果並非永久
>- 系統重新啟動後，swap 仍會重新開啟
>- 需要執行下方的操作，避免開機時啟用 Swap

### 永久關閉 Swap
修改 fstab，避免開機時掛載 swap 磁區
- 使用文字編輯器 (nano, vim, …)開啟 /etc/fstab
```shell
sudo vim /etc/fstab
# 或
sudo nano /etc/fstab
```

### 安裝containerd、kubeadm、kubelet、kubectl
在所有節點上執行下面的命令安裝containerd和一些必要的工具。
```shell
curl -fsSL https://raw.githubusercontent.com/xiaopeng163/learn-k8s-from-scratch/master/source/_code/k8s-install/install.sh -o install.sh
sudo sh install.sh
```

運行後，我們可以查看可用的版本

```shell
tai@tai-virtual-machine:~$ apt list -a kubeadm
Listing... Done
kubeadm/unknown 1.29.6-1.1 amd64 [upgradable from: 1.29.2-1.1]
kubeadm/unknown 1.29.5-1.1 amd64
kubeadm/unknown 1.29.4-2.1 amd64
kubeadm/unknown 1.29.3-1.1 amd64
kubeadm/unknown,now 1.29.2-1.1 amd64 [installed,upgradable to: 1.29.6-1.1]
kubeadm/unknown 1.29.1-1.1 amd64
kubeadm/unknown 1.29.0-1.1 amd64

kubeadm/unknown 1.29.6-1.1 arm64
kubeadm/unknown 1.29.5-1.1 arm64
kubeadm/unknown 1.29.4-2.1 arm64
kubeadm/unknown 1.29.3-1.1 arm64
kubeadm/unknown 1.29.2-1.1 arm64
kubeadm/unknown 1.29.1-1.1 arm64
kubeadm/unknown 1.29.0-1.1 arm64

kubeadm/unknown 1.29.6-1.1 ppc64el
kubeadm/unknown 1.29.5-1.1 ppc64el
kubeadm/unknown 1.29.4-2.1 ppc64el
kubeadm/unknown 1.29.3-1.1 ppc64el
kubeadm/unknown 1.29.2-1.1 ppc64el
kubeadm/unknown 1.29.1-1.1 ppc64el
kubeadm/unknown 1.29.0-1.1 ppc64el

kubeadm/unknown 1.29.6-1.1 s390x
kubeadm/unknown 1.29.5-1.1 s390x
kubeadm/unknown 1.29.4-2.1 s390x
kubeadm/unknown 1.29.3-1.1 s390x
kubeadm/unknown 1.29.2-1.1 s390x
kubeadm/unknown 1.29.1-1.1 s390x
kubeadm/unknown 1.29.0-1.1 s390x
```

在所有節點上執行下面的命令安裝kubeadm/kubelet/kubectl，確保版本一致。

```shell
sudo apt install  -y kubeadm=1.29.2-1.1 kubelet=1.29.2-1.1 kubectl=1.29.2-1.1
```

可以檢查下kubeadm，kubelet，kubectl的安裝情況，如果能夠取得到版本號，說明安裝成功。

```shell
kubeadm version
kubelet --version
kubectl version
```
### 初始化master節點
初始化Kubeadm
- `--apiserver-advertise-address` 這個位址是本地用於和其他節點通訊的IP位址
- `--pod-network-cidr` pod 網路位址空間
```shell
vagrant@k8s-master:~$ sudo kubeadm init --apiserver-advertise-address=<your_master_ip>  --pod-network-cidr=10.244.0.0/16
```
>[!info]
>此處的10.244.0.0/16 為 flannel 的容器網路結構。

接著他會輸出一些內容其中 `token` 要保存好，增加 worker 時會需要使用到。
- 1. 準備.kube
- 2. 配置pod網路方案
- 3. 新增工人節點
```shell
  Your Kubernetes control-plane has initialized successfully!

  To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

  Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

  You should now deploy a pod network to the cluster.
  Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

  Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.56.10:6443 --token 0pdoeh.wrqchegv3xm3k1ow \
  --discovery-token-ca-cert-hash sha256:f4e693bde148f5c0ff03b66fb24c51f948e295775763e8c5c4e60d24ff57fe82
```
### 配置.kube
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

查看狀態：
```shell
kubectl get nodes
kubectl get pods -A
```

輸出
```shell
vagrant@k8s-master:~$ kubectl get nodes -o wide
NAME         STATUS     ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
k8s-master   NotReady   control-plane   3m10s   v1.29.2   10.211.55.4   <none>        Ubuntu 22.04.2 LTS   5.15.0-76-generic   containerd://1.6.28
vagrant@k8s-master:~$ kubectl get pod -A
NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE
kube-system   coredns-76f75df574-4l8fv             0/1     Pending   0          3m18s
kube-system   coredns-76f75df574-ztqqx             0/1     Pending   0          3m18s
kube-system   etcd-k8s-master                      1/1     Running   0          3m32s
kube-system   kube-apiserver-k8s-master            1/1     Running   0          3m32s
kube-system   kube-controller-manager-k8s-master   1/1     Running   0          3m33s
kube-system   kube-proxy-4mzl6                     1/1     Running   0          3m18s
kube-system   kube-scheduler-k8s-master            1/1     Running   0          3m32s
```

## Kubeadm 本地搭建集群的驗證
### Kubeadm 補全功能
輸入完這個指令之後，接著後面就可以使用 `kubectl` 的補全功能，會告知你目前指令後面可以加那些指令。
```shell
source <(kubectl completion bash)
```
### Start
三節點環境搭建完成後，可以透過下面的方式快速驗證環境是否搭建成功。
```shell
kubectl get nodes -o wide
```

輸出
```shell
NAME          STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
k8s-master    Ready    control-plane   38m   v1.29.2   10.211.55.4   <none>        Ubuntu 22.04.2 LTS   5.15.0-76-generic   containerd://1.6.28
k8s-worker1   Ready    <none>          21m   v1.29.2   10.211.55.5   <none>        Ubuntu 22.04.2 LTS   5.15.0-97-generic   containerd://1.6.28
k8s-worker2   Ready    <none>          21m   v1.29.2   10.211.55.6   <none>        Ubuntu 22.04.2 LTS   5.15.0-76-generic   containerd://1.6.28
```
#### 創建 Pod
建立一個 nginx 的 pod
```shell
kubectl run web --image nginx
```

成功訊息
```shell
pod/web created
```

顯示所有Pod 的詳細信息
```shell
kubectl get pods -o wide
```

輸出
```shell
NAME   READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
web    1/1     Running   0          63s   10.244.1.2   k8s-worker1   <none>           <none>
```
#### 創建服務
給nginx pod建立一個服務，透過curl可以存取這個服務的叢集ip位址。
```shell
kubectl expose pod web  --port=80 --name=web-service
```

成功訊息
```shell
service/web-service exposed
```

顯示目前Kubernetes 叢集中所有服務（Services）的資訊
```shell
kubectl get service
```

輸出
```shell
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP   3h53m
web-service   ClusterIP   10.98.102.238   <none>        80/TCP    4s
```

獲取 nginx 頁面，驗證是否成功 !
```shell
curl your-control-planel-ip:port
```
### 環境清理
```shell
$ kubectl delete service web-service
$ kubectl delete pod web
```
