---
Date: 2024-07-27
---
>[!info]
>12

---
這次會繼承上一篇[[K8s - Exposing a Single Service with Ingress]]的建置繼續進行下去 !
### 創建 Web 服務器部署和服務
- `kubectl create deployment demo --image=httpd --port=80`：創建了一個名為 `demo` 的部署，使用 `httpd` 鏡像，並將容器內的 80 端口暴露出來。
- `kubectl expose deployment demo --type=ClusterIP --port=80`：將 `demo` 部署暴露為一個服務，類型為 `ClusterIP`，並將 80 端口暴露出來。
```shell
tai@tai-virtual-machine:~$ kubectl create deployment demo --image=httpd --port=80
deployment.apps/demo created
tai@tai-virtual-machine:~$ kubectl expose deployment demo
service/demo exposed
tai@tai-virtual-machine:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
demo         ClusterIP   10.101.99.122   <none>        80/TCP    10s
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   42d
```

成功的話，會輸出 'It works!'

```shell
tai@tai-virtual-machine:~$ curl 10.101.99.122
<html><body><h1>It works!</h1></body></html>
tai@tai-virtual-machine:~$
```
### 創建 Ingress 資源
創建一個 Ingress 資源來路由到 `demo` 服務：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-localhost
  namespace: default
spec:
  ingressClassName: nginx
  rules:
  - host: demo.localdev.me
    http:
      paths:
      - backend:
          service:
            name: demo
            port:
              number: 80
        path: /
        pathType: Prefix
```
>[!info]
>- `ingressClassName: nginx`：指定使用 NGINX Ingress Controller。
>- `host: demo.localdev.me`：定義當訪問 `demo.localdev.me` 時，將流量路由到 `demo` 服務。
>- `service: name: demo, port: number: 80`：指定要路由到的服務及其端口。
### 獲取服務的 ClusterIP
你已經成功獲取了 `demo` 服務和 `ingress-nginx-controller` 服務的 ClusterIP：
```shell
tai@tai-virtual-machine:~$ kubectl get svc
tai@tai-virtual-machine:~$ kubectl get svc --namespace ingress-nginx
```
![[K8s - Exposing a Single Service with Ingress 1.png]]

直接訪問 `demo` 的 `clusterIP` 是可以的
```shell
tai@tai-virtual-machine:~$ curl 10.101.99.122
<html><body><h1>It works!</h1></body></html>
```

但是直接访问ingress-nginx-controller的ClusterIP是不可以的

```shell
tai@tai-virtual-machine:~$ curl  10.108.109.142
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

需要透過網域存取, 當然前提是要把網域對應的 `ingress-nginx-controller` 的 `ClusterIP` 放到系統 `hosts` 檔案裡。以這個實例來說，也就是 `10.108.109.142 demo.localdev.me` 。

```shell
tai@tai-virtual-machine:~$ sudo vim /etc/hosts
tai@tai-virtual-machine:~$ curl demo.localdev.me
<html><body><h1>It works!</h1></body></html>
```
### 架構圖
![[K8s - Exposing a Single Service with Ingress 2.png]]