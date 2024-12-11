---
Date: 2024-07-24
tags:
  - k8s
  - devops
---
>[!info]
>源碼 :  [https://github.com/xiaopeng163/flask-redis](https://github.com/xiaopeng163/flask-redis)

在 Kubernetes 中，服務發現是指如何讓 Pod 能夠互相找到並通信。Kubernetes 提供了多種服務發現機制：

1. **環境變量**： 當一個 Pod 啟動時，Kubernetes 會自動為該 Pod 中的容器設置與其他服務相關的環境變量。這些環境變量包含服務的集群 IP 和端口號，但這種方法在動態服務發現中不夠靈活。
2. **DNS**： Kubernetes 通過 kube-dns 或 CoreDNS 提供內部 DNS 解決方案。這種方法允許 Pod 使用服務名稱來解析服務的集群 IP。以下是使用 DNS 進行服務發現的過程：
    - 每個 Kubernetes 服務都會在集群內部 DNS 伺服器中註冊一個 DNS 條目。
    - Pod 可以通過 `service-name.namespace.svc.cluster.local` 的形式來訪問服務。
    - 例如，有一個名為 `my-service` 的服務在 `default` 命名空間中，Pod 可以通過 `my-service.default.svc.cluster.local` 來訪問該服務。
### 配置示例
Kubernetes 中服務的自動發現配置示例，並以 Redis 和 Flask 來演示。
#### Redis 部署與服務配置
首先，建立 Redis 部署和服務配置：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-server
  template:
    metadata:
      labels:
        app: redis-server
    spec:
      containers:
        - name: redis-server
          image: redis:latest
          command:
            - redis-server
            - --requirepass
            - redis
          ports:
            - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  selector:
    app: redis-server
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
```
#### Flask 部署與服務配置
接著，建立 Flask 應用的部署和服務配置：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
        - name: flask-app
          image: xiaopeng163/flask-redis
          ports:
            - containerPort: 5000
          env:
            - name: REDIS_HOST
              value: <???????????>
---
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 5000
```

我們將他們合成一個 `yml` 檔案，來 `apply` ，但在這之前我們需要注意一下，value 這個值，由於我們的 `flask` 有使用 `redis` 所以他是必要知道 `redis` 的地址，我們可以透過 `DNS` 或是 `ENV` 的方式。

我們在還未創建之前式部會知道 `redis service clusterIP` 的，我們也不可能先創建 `redis` 然後再寫在 `apply` 這樣有點麻煩，這是我們就可以使用 `service discovery` 主要有兩種方式。1. `DNS` 2. `ENV`。
### DNS
在之前有介紹在這個集群中 `CoreDNS` 有這樣一個 `DNS` 有一種服務式提供域名，而只要我們 `service name` 決定了，那這個域名就決定了。

也就是 `<service name>.<namespace>.svc.cluster.local`

```shell
tai@tai-virtual-machine:~$ kubectl apply -f flask-redis.yml
deployment.apps/redis-deployment created
service/redis created
deployment.apps/flask-deployment created
service/flask-service created
```
### ENV
如果要使用 `env` 方式，我們則需要在 `Flask` 連接 `redis` 所獲取的環境變數就要先設置好，`k8s` 自動幫你創建的 `redis` 環境變數名稱。

我們可以看到使用指令創建後立即進入 `pod` 中查看，`k8s` 自動生成這些環境變數，也就是說我們需要在程式中連線的環境變量名稱更改為`REDIS_PORT_6379_TCP_ADDR`。

```shell
tai@tai-virtual-machine:~$ kubectl run client --image=xiaopeng163/net-box --command -- sh -c "sleep 100000"
pod/client created
tai@tai-virtual-machine:~$ kubectl exec -it client -- sh
/omd # env | grep DEMO
/omd # env | grep REDIS
REDIS_SERVICE_PORT=6379
REDIS_PORT=tcp://10.110.196.183:6379
REDIS_PORT_6379_TCP_ADDR=10.110.196.183
REDIS_PORT_6379_TCP_PORT=6379
REDIS_PORT_6379_TCP_PROTO=tcp
REDIS_PORT_6379_TCP=tcp://10.110.196.183:6379
REDIS_SERVICE_HOST=10.110.196.183
```
#### 測試服務發現
我們可以通過創建和暴露示範應用來測試服務發現：

```shell
tai@tai-virtual-machine:~$ kubectl get svc
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
flask-service   ClusterIP   10.109.168.214   <none>        8000/TCP   15s
kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP    39d
redis           ClusterIP   10.110.196.183   <none>        6379/TCP   15s
tai@tai-virtual-machine:~$ curl 10.109.168.214:8000
Hello Container World! I have been seen 1 times and my hostname is flask-deployment-66bfcb8fd7-72tgf.
tai@tai-virtual-machine:~$ curl 10.109.168.214:8000
Hello Container World! I have been seen 2 times and my hostname is flask-deployment-66bfcb8fd7-72tgf.
tai@tai-virtual-machine:~$
```
