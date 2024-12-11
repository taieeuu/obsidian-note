---
Date: 2024-07-23
---
>[!info]
>[https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)

當我們成功初始化K8S，這個集群裡面已經運行了許多的 `pod` 或是 `service`，其中有一些是跟 `DNS` 相關的。

在K8S中，DNS 服務是以服務的形式提供，並且 Pods 被配置為使用這個 DNS。
### 叢集 DNS 配置
- **1 個 deployment（1 個 replicaset），其中包含 2 個在 master 節點上運行的 Pods**
- **1 個 kube-dns 服務**
### 查看叢集中的所有 DNS 組件
```shell
tai@tai-virtual-machine:~$ kubectl get all -A -o wide | grep dns
kube-system    pod/coredns-76f75df574-45xrd                      1/1     Running   4 (2d12h ago)   38d   10.244.0.14       tai-virtual-machine   <none>           <none>
kube-system    pod/coredns-76f75df574-p86l4                      1/1     Running   4 (2d12h ago)   38d   10.244.0.13       tai-virtual-machine   <none>           <none>
kube-system   service/kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   38d   k8s-app=kube-dns
kube-system   deployment.apps/coredns   2/2     2            2           38d   coredns      registry.k8s.io/coredns/coredns:v1.11.1   k8s-app=kube-dns
kube-system   replicaset.apps/coredns-76f75df574   2         2         2       38d   coredns      registry.k8s.io/coredns/coredns:v1.11.1   k8s-app=kube-dns,pod-template-hash=76f75df574
tai@tai-virtual-machine:~$
```
### 查看 CoreDNS 配置
```shell
tai@tai-virtual-machine:~$  kubectl describe configmaps coredns --namespace kube-system
Name:         coredns
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
Corefile:
----
.:53 {
    errors
    health {
       lameduck 5s
    }
    ready
    kubernetes cluster.local in-addr.arpa ip6.arpa {
       pods insecure
       fallthrough in-addr.arpa ip6.arpa
       ttl 30
    }
    prometheus :9153
    forward . /etc/resolv.conf {
       max_concurrent 1000
    }
    cache 30
    loop
    reload
    loadbalance
}


BinaryData
====

Events:  <none>
```
### Pod DNS 設置
##### 1. 建立一個名為 `client` 的 Pod
```shell
kubectl run client --image=xiaopeng163/net-box --command -- sh -c "sleep 100000"
```
##### 2. 查看 `client` Pod 的 `/etc/resolv.conf` 文件
這是 `DNS` 的配置
- `search default.svc.cluster.local svc.cluster.local cluster.local localdomain`: 這些是 DNS 查詢搜索域，當在 Pod 中查詢一個主機名時，Kubernetes 會依次嘗試這些域。
- `nameserver 10.96.0.10`: 這是 Kubernetes 叢集 DNS 服務的 IP 地址，用於解析域名。
- `options ndots:5`: 這個選項指定了在 DNS 查詢中需要有多少個點（`.`）才會被視為完全限定域名（FQDN）。如果沒有這麼多點，搜索域將會被附加到查詢名上。

```shell
tai@tai-virtual-machine:~$ kubectl exec -it client -- sh
/omd # cat /etc/resolv.conf
search default.svc.cluster.local svc.cluster.local cluster.local localdomain
nameserver 10.96.0.10
options ndots:5
```
### Pod 的 A/AAAA 記錄
一般來說，一個 Pod 有以下的 DNS 解析：

`pod-ip-address.my-namespace.pod.cluster-domain.example`
### 使用 nslookup 查詢 Pod 的 DNS 記錄
```shell
tai@tai-virtual-machine:~$ kubectl exec -it client -- sh
/omd # nslookup 10-244-2-105.default.pod.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   10-244-2-105.default.pod.cluster.local
Address: 10.244.2.105
```

如果不是在`pod`裡面的話，會需要使用本地的 `DNS` 也就是說需要在後面增加 `pod's DNS` 的 `ip`

```shell
tai@tai-virtual-machine:~$ nslookup 10-244-2-105.default.pod.cluster.local 10.96.0.10
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   10-244-2-105.default.pod.cluster.local
Address: 10.244.2.105
```
