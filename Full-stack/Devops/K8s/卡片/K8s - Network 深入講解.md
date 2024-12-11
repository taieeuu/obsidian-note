---
Date: 2024-07-22
tags:
  - k8s
  - devops
---
## Pod 之間通信（單節點）
創建測試 Pod 的 YAML 文件：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod1
spec:
  nodeName: 'k8s-worker1' # 指定节点，请根据实际情况修改
  containers:
  - name: pod1
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod2
spec:
  nodeName: 'k8s-worker1' # 指定节点，请根据实际情况修改
  containers:
  - name: pod2
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
```
### 創建 Pod 之前的網絡配置
在創建 Pod 之前，我們可以檢查 `k8s-worker1` 節點的網絡配置：

```shell
tai@k8s-worker1:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:35:0f:f5 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.128/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 1660sec preferred_lft 1660sec
    inet6 fe80::175c:ce93:23f9:7680/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether aa:9d:53:f9:51:dd brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::a89d:53ff:fef9:51dd/64 scope link
       valid_lft forever preferred_lft forever
```
### 創建 Pod 並檢查網絡配置
創建 Pod：

```shell
kubectl apply -f pod-to-pod-single-node.yml
```

檢查 Pod 的狀態和 IP 地址：

```shell
tai@tai-virtual-machine:~$ kubectl get pods -o wide
NAME     READY   STATUS    RESTARTS   AGE   IP            NODE          NOMINATED NODE   READINESS GATES
mypod1   1/1     Running   0          25s   10.244.1.26   k8s-worker1   <none>           <none>
mypod2   1/1     Running   0          25s   10.244.1.25   k8s-worker1   <none>           <none>
tai@tai-virtual-machine:~$
```

創建 Pod 之後，我們再次檢查 `k8s-worker1` 節點的網絡配置，我們可以發現多了 `cni0`、`vethccb6c1a4`、`veth2d0d027c` 這些網絡接口。

```shell
tai@k8s-worker1:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:35:0f:f5 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.128/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 1516sec preferred_lft 1516sec
    inet6 fe80::175c:ce93:23f9:7680/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether aa:9d:53:f9:51:dd brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::a89d:53ff:fef9:51dd/64 scope link
       valid_lft forever preferred_lft forever
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether 0a:c6:62:21:46:03 brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.1/24 brd 10.244.1.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::8c6:62ff:fe21:4603/64 scope link
       valid_lft forever preferred_lft forever
5: vethccb6c1a4@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default
    link/ether a6:f4:a2:e6:dc:9f brd ff:ff:ff:ff:ff:ff link-netns cni-89aefc12-54dc-4d1f-220d-eefeb92e68e2
    inet6 fe80::a4f4:a2ff:fee6:dc9f/64 scope link
       valid_lft forever preferred_lft forever
6: veth2d0d027c@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default
    link/ether 06:e6:93:f6:fd:c7 brd ff:ff:ff:ff:ff:ff link-netns cni-75d3d014-a093-058d-5c9a-254d6152f374
    inet6 fe80::4e6:93ff:fef6:fdc7/64 scope link
       valid_lft forever preferred_lft forever
```
### 檢查網橋配置
在 `k8s-worker1` 節點上檢查網橋配置，我們可以將 `cni0` 看作是一個集線器（hub），將 `veth2d0d027c` 和 `vethccb6c1a4` 這兩個虛擬以太網接口連接在一起。

```shell
tai@k8s-worker1:~$ brctl show
bridge name     bridge id               STP enabled     interfaces
cni0            8000.0ac662214603       no              veth2d0d027c
                                                        vethccb6c1a4
```

在這個配置中，`cni0` 作為網橋，將 `veth2d0d027c` 和 `vethccb6c1a4` 這兩個虛擬網卡接口連接起來，使它們能夠相互通信。這種配置方式有助於實現 Pod 之間以及 Pod 與宿主機之間的網絡互通。
## Pod 之間通信（多節點）
創建測試 Pod 的 YAML 文件：

**pod.yml**
```shell
apiVersion: v1
kind: Pod
metadata:
  name: mypod1
spec:
  nodeName: 'k8s-worker1'
  containers:
  - name: pod1
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod2
spec:
  nodeName: 'k8s-worker2'
  containers:
  - name: pod2
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
```
### 在 worker1 節點上檢查網路接口
```shell
tai@k8s-worker1:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:35:0f:f5 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.128/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 1470sec preferred_lft 1470sec
    inet6 fe80::175c:ce93:23f9:7680/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether aa:9d:53:f9:51:dd brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::a89d:53ff:fef9:51dd/64 scope link
       valid_lft forever preferred_lft forever
4: cni0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 0a:c6:62:21:46:03 brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.1/24 brd 10.244.1.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::8c6:62ff:fe21:4603/64 scope link
       valid_lft forever preferred_lft forever
```
### 在 worker2 節點上檢查網路接口
```shell
tai2@k8s-worker2:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:65:8a:33 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.130/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 1464sec preferred_lft 1464sec
    inet6 fe80::1844:dff:9c3c:f091/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether 36:a8:fd:e4:29:e7 brd ff:ff:ff:ff:ff:ff
    inet 10.244.2.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::34a8:fdff:fee4:29e7/64 scope link
       valid_lft forever preferred_lft forever
4: cni0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 92:59:28:eb:e9:ee brd ff:ff:ff:ff:ff:ff
    inet 10.244.2.1/24 brd 10.244.2.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::9059:28ff:feeb:e9ee/64 scope link
       valid_lft forever preferred_lft forever
```
### 創建 Pod
```shell
kubectl apply -f pod.yml
```
### 檢查 Pod 狀態
```shell
tai@tai-virtual-machine:~$ kubectl get pods -o wide
NAME     READY   STATUS    RESTARTS   AGE   IP             NODE          NOMINATED NODE   READINESS GATES
mypod1   1/1     Running   0          5s    10.244.1.27    k8s-worker1   <none>           <none>
mypod2   1/1     Running   0          5s    10.244.2.104   k8s-worker2   <none>           <none>
```
### 創建後在 worker1 節點上檢查網路接口
```shell
tai@k8s-worker1:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:35:0f:f5 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.128/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 930sec preferred_lft 930sec
    inet6 fe80::175c:ce93:23f9:7680/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether aa:9d:53:f9:51:dd brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::a89d:53ff:fef9:51dd/64 scope link
       valid_lft forever preferred_lft forever
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether 0a:c6:62:21:46:03 brd ff:ff:ff:ff:ff:ff
    inet 10.244.1.1/24 brd 10.244.1.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::8c6:62ff:fe21:4603/64 scope link
       valid_lft forever preferred_lft forever
7: veth675abef5@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default
    link/ether 82:c8:bf:cb:c8:6d brd ff:ff:ff:ff:ff:ff link-netns cni-f7a57856-d1ab-06c5-b964-4777d3215846
    inet6 fe80::80c8:bfff:fecb:c86d/64 scope link
       valid_lft forever preferred_lft forever
```
### 創建後在 worker2 節點上檢查網路接口
```shell
tai2@k8s-worker2:~$ ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:65:8a:33 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.118.130/24 brd 192.168.118.255 scope global dynamic noprefixroute ens33
       valid_lft 925sec preferred_lft 925sec
    inet6 fe80::1844:dff:9c3c:f091/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether 36:a8:fd:e4:29:e7 brd ff:ff:ff:ff:ff:ff
    inet 10.244.2.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::34a8:fdff:fee4:29e7/64 scope link
       valid_lft forever preferred_lft forever
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether 92:59:28:eb:e9:ee brd ff:ff:ff:ff:ff:ff
    inet 10.244.2.1/24 brd 10.244.2.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::9059:28ff:feeb:e9ee/64 scope link
       valid_lft forever preferred_lft forever
11: veth150bfa51@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default
    link/ether ca:2b:a3:a3:b3:dd brd ff:ff:ff:ff:ff:ff link-netns cni-b74e7c0d-a96a-0d1b-d2e5-6133d5a94eae
    inet6 fe80::c82b:a3ff:fea3:b3dd/64 scope link
       valid_lft forever preferred_lft forever
```
###  Pod 之間的通信
如果我們要使他們兩個之間通信，必須要經過 `ens33` worker1 與 worker2 這兩個網卡是可以相互 ping 通的，具體的實現是因為通過了 `flannel` 來使用 `VXLAN` 封裝。在通過 `ens33` 這個地放去傳遞數據。

要記得我們`flannel`是在每一台機器上面都會有安裝的
```shell
kubectl get pods -A
```
### 補充說明
1. **Flannel**：Flannel 是一個簡單且易於配置的網絡插件，用於 Kubernetes 的 Pod 網絡。它通過分配每個節點一個子網來確保 Pod 在不同節點之間的 IP 地址唯一性。Flannel 支持多種後端，包括 VXLAN、UDP、AWS VPC、GCE 路由等。在本例中，我們使用的是 VXLAN。
2. **VXLAN**：VXLAN（Virtual Extensible LAN）是一種網絡虛擬化技術，用於在數據中心內部創建二層網絡。它使用封裝技術在三層網絡上傳輸二層流量，從而實現跨數據中心的二層擴展。Flannel 使用 VXLAN 作為後端來實現跨節點的 Pod 網絡。
3. **Pod 通信驗證**：創建並運行 Pod 之後，可以使用 `kubectl exec` 命令進入其中一個 Pod 並 ping 另一個 Pod 來驗證它們之間的通信。例如，進入 `mypod1` 並 ping `mypod2`：
	```shell
	kubectl exec -it mypod1 -- ping 10.244.2.104
	```