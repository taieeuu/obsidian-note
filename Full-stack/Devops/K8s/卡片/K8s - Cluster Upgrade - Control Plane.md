---
Date: 2024-07-28
---
---
>[!info]
>changelog : [https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG)


升級現有的 Kubernetes 叢集，特別是使用 kubeadm 建立的叢集，只能進行小版本更新，因要確保相容性與穩定性，在升級前閱讀 [`changelog`]([https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG))，使用**漸進式變更**版本。
###### 檢查當前版本：
```shell
tai@tai-virtual-machine:~$ kubectl version
Client Version: v1.29.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.6
tai@tai-virtual-machine:~$ kubectl get nodes
NAME                  STATUS                     ROLES           AGE   VERSION
k8s-worker1           Ready,SchedulingDisabled   <none>          43d   v1.29.2
k8s-worker2           Ready                      <none>          43d   v1.29.2
tai-virtual-machine   Ready                      control-plane   43d   v1.29.2
tai@tai-virtual-machine:~$ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"29", GitVersion:"v1.29.2", GitCommit:"4b8e819355d791d96b7e9d9efe4cbafae2311c88", GitTreeState:"clean", BuildDate:"2024-02-14T10:39:04Z", GoVersion:"go1.21.7", Compiler:"gc", Platform:"linux/amd64"}
tai@tai-virtual-machine:~$
```
### Upgrade Control Plane
#### 更新 kubeadm 套件
首先先 `unhold` 讓它不鎖定住版本，使它可以進行更新。

```shell
tai@tai-virtual-machine:~$ sudo apt-mark unhold kubeadm
kubeadm was already not on hold.
```
###### 顯示 `kubeadm` 套件的可用版本和當前已安裝的版本
```shell
tai@tai-virtual-machine:~$ sudo apt-cache policy kubeadm
kubeadm:
  Installed: 1.29.2-1.1
  Candidate: 1.29.7-1.1
  Version table:
     1.29.7-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
     1.29.6-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
     1.29.5-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
     1.29.4-2.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
     1.29.3-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
 *** 1.29.2-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
        100 /var/lib/dpkg/status
     1.29.1-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
     1.29.0-1.1 500
        500 https://pkgs.k8s.io/core:/stable:/v1.29/deb  Packages
```
###### 更新版本，**注意**: 進行更新請不要一次更新大版本，盡量使用循序漸進的版本更新。
```shell
sudo apt-get install -y kubeadm=1.29.7-1.1
```
###### 鎖住 `kubeadm` 版本
```shell
sudo apt-mark hold kubeadm
```
#### drain master node
###### 遷移至其他節點：
將 Kubernetes 節點 `tai-virtual-machine` 上的工作負載排空，以便進行維護、升級或其他操作

```shell
tai@tai-virtual-machine:~$ kubectl drain tai-virtual-machine --ignore-daemonsets
node/tai-virtual-machine cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-lgfh4, kube-system/kube-proxy-sp86l
evicting pod kube-system/coredns-76f75df574-p86l4
evicting pod kube-system/coredns-76f75df574-45xrd
pod/coredns-76f75df574-p86l4 evicted
pod/coredns-76f75df574-45xrd evicted
node/tai-virtual-machine drained
tai@tai-virtual-machine:~$
```
###### 生成升級計劃
![[K8s - Cluster Upgrade - Control Plane 1.png]]

使用上述的提示進行升級，記得前面要加上 `sudo`

```shell
tai@tai-virtual-machine:~$ sudo kubeadm upgrade apply v1.29.7
```
#### 解鎖節點：
```shell
tai@tai-virtual-machine:~$ kubectl uncordon tai-virtual-machine
node/tai-virtual-machine uncordoned
```
#### Update kubelet and kubectl
```shell
tai@tai-virtual-machine:~$ sudo apt-mark unhold kubelet kubectl
[sudo] password for tai:
kubelet was already not on hold.
kubectl was already not on hold.
tai@tai-virtual-machine:~$ sudo apt-get update
tai@tai-virtual-machine:~$ sudo apt-get install -y kubelet=1.29.7-1.1 kubectl=1.29.7-1.1
tai@tai-virtual-machine:~$ sudo apt-mark hold kubelet kubectl
kubelet set on hold.
kubectl set on hold.
```
### 驗證
從`1.29.2`升級至`1.29.7`

```shell
tai@tai-virtual-machine:~$ kubectl version
Client Version: v1.29.7
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.7
tai@tai-virtual-machine:~$ kubelet --version
Kubernetes v1.29.7
```