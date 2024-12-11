---
Date: 2024-07-27
---
---
繼承上一篇 [[K8s - New User Certificate and Key]]
### kubeconfig 文件管理
#### 添加新用戶到 kubeconfig 文件
**新增 `demouser` 用戶**：
由於上一篇我們有了1. 名稱 2. CSR 3. Base64的簽名值

```shell
tai@tai-virtual-machine:~$ kubectl config set-credentials demouser --client-key=demouser.key --client-certificate=demouser.crt --embed-certs=true
User "demouser" set.
tai@tai-virtual-machine:~$
```

**檢查用戶並查看Config**
可以看到我們已經成功創建兩個 `User` ，接下來我們要使用我們新的 `User` 去使用 `Contexts`。

```shell
tai@tai-virtual-machine:~$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.168.118.129:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    namespace: default
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: demouser
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
- name: kubernetes-admin
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
tai@tai-virtual-machine:~$
```

**創建一個 Context，並切換至它的空間**
當你切換至 `demo context` 後，`k8s` 就會透過 `demouser` 去連接我們的`api service`。

```shell
tai@tai-virtual-machine:~$ kubectl config set-context demo --user=demouser --cluster=kubernetes
Context "demo" created.
tai@tai-virtual-machine:~$ kubectl config use-context demo
Switched to context "demo".
tai@tai-virtual-machine:~$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         demo                          kubernetes   demouser
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   default
tai@tai-virtual-machine:~$
```

**使用 demouser 查看 pod**
你會發現你沒有權限查看，在 `k8s` 中，會戶的管理分為兩種，在一開始的 `Overview` 有說明到 1. 你是誰 2. 你可以做什麼。

```shell
tai@tai-virtual-machine:~$ kubectl get pod
Error from server (Forbidden): pods is forbidden: User "demouser" cannot list resource "pods" in API group "" in the namespace "default"
tai@tai-virtual-machine:~$
```
