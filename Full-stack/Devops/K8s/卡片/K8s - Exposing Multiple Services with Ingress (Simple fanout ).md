---
Date: 2024-07-27
---
---
![[K8s - Exposing Multiple Services with Ingress (Simple fanout ) 1.png|700]]

繼承上一篇[[K8s - Ingress Controller]]的建置繼續進行下去 !
### 創建兩個部署和服務
1. 創建 `web1` 部署和服務：

```shell
tai@tai-virtual-machine:~$ kubectl create deployment web1 --image=gcr.io/google-samples/hello-app:1.0 --port=8080 --replicas=2
deployment.apps/web1 created
tai@tai-virtual-machine:~$ kubectl expose deployment web1 --port 9001 --target-port 8080
service/web1 exposed
```

2. 創建 `web2` 部署和服務：

```shell
tai@tai-virtual-machine:~$ kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0 --port=8080 --replicas=2
deployment.apps/web2 created
tai@tai-virtual-machine:~$  kubectl expose deployment web2 --port 9002 --target-port 8080
service/web2 exposed
tai@tai-virtual-machine:~$
```

3. 檢查部署和服務：

```shell
tai@tai-virtual-machine:~$ kubectl get deployments.apps
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
web1   2/2     2            2           106s
web2   2/2     2            2           94s
tai@tai-virtual-machine:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    42d
web1         ClusterIP   10.101.6.2      <none>        9001/TCP   106s
web2         ClusterIP   10.105.30.218   <none>        9002/TCP   95s
```
### 創建 Ingress 資源
創建一個 Ingress 資源來路由到不同的服務：

```shell
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-multiple
spec:
  ingressClassName: nginx
  rules:
    - host: api.example.com
      http:
        paths:
        - path: /v1
          pathType: Prefix
          backend:
            service:
              name: web1
              port:
                number: 9001
        - path: /v2
          pathType: Prefix
          backend:
            service:
              name: web2
              port:
                number: 9002
  defaultBackend:
    service:
      name: web1
      port:
        number: 9001
```

保存為 `ingress-multiple.yaml`，然後應用：

```shell
tai@tai-virtual-machine:~$ kubectl apply -f ingress.yml
ingress.networking.k8s.io/ingress-multiple created
```
### 檢查 Ingress 狀態
查看 Ingress 資源：
```shell
tai@tai-virtual-machine:~$ kubectl get ingress
NAME               CLASS   HOSTS             ADDRESS   PORTS   AGE
ingress-multiple   nginx   api.example.com             80      8s
```

![[K8s - Exposing Multiple Services with Ingress (Simple fanout ) 2.png]]
### 獲取 Service
這邊為何使用 `-A` 參數呢，因為這個 `ingress controller` 在`ingress-nginx` 的 `namespaces` 下，所以我們使用獲取全部的 `namespaces`。

![[K8s - Exposing Multiple Services with Ingress (Simple fanout ) 3.png]]
### 設置 `/etc/hosts` 文件
將 `api.example.com` 映射到 `ingress-nginx-controller` 的 ClusterIP 或節點 IP，也就是添加 `ingress Type LoadBalancer`的 `IP` 映射到 `api.example.com`。

```shell
sudo vim /etc/hosts
```
### 驗證
```shell
tai@tai-virtual-machine:~$ curl api.example.com
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-m2h24
tai@tai-virtual-machine:~$ curl api.example.com/v1
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-m2h24
tai@tai-virtual-machine:~$ curl api.example.com/v2
Hello, world!
Version: 2.0.0
Hostname: web2-7bb99d547c-bsq8m
tai@tai-virtual-machine:~$
```
### 清除資源
我們保留 `svc` 與 `devployments.apps` 並刪除 `ingress`。
