---
Date: 2024-07-28
---
---
在 Kubernetes 中，所有的集群都有兩類使用者：

1. **服務帳號 (Service Accounts)**：由 Kubernetes 管理，用於程序 (Pod) 連接 API 伺服器。
2. **普通使用者 (Normal Users)**：例如透過 `kubectl` 連接 API 伺服器的使用者。
### 建立服務帳號 (ServiceAccount)
```shell
$ kubectl create serviceaccount demosa
$ kubectl describe serviceaccount demosa
```
### 在 Pod 中檢查服務帳號
###### 建立一個 Pod：
**pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: client
spec:
   serviceAccount: demosa
   containers:
   - name: client
     image: xiaopeng163/net-box:latest
     command:
      - sh
      - -c
      - "sleep 1000000"

```

```shell
kubectl apply -f pod.yaml
```
###### 檢查服務帳號的 token：
可以使用 `| jq` or `python3 -m json.tool` 來美化排版。

```shell
tai@tai-virtual-machine:~$ kubectl get pods client -o jsonpath='{.spec.containers[0].volumeMounts}' | python3 -m json.tool
[
    {
        "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
        "name": "kube-api-access-cm9ng",
        "readOnly": true
    }
]
tai@tai-virtual-machine:~$ kubectl get pods client -o jsonpath='{.spec.containers[0].volumeMounts}' | jq
[
  {
    "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
    "name": "kube-api-access-cm9ng",
    "readOnly": true
  }
]
tai@tai-virtual-machine:~$
```



解釋參數: 
- **ca.crt**：
    - **說明**：這是 Kubernetes 集群的 CA 憑證 (Certificate Authority)。
    - **用途**：用於在與 Kubernetes API Server 通信時驗證 API Server 的身份，以確保通信是與正確的 API Server 進行的。
- **namespace**：
    - **說明**：這個文件包含 Pod 所屬的命名空間 (Namespace) 名稱。
    - **用途**：Pod 使用這個文件來確定自己所處的命名空間，這對於某些 API 請求非常重要，因為許多 Kubernetes 資源都是命名空間範圍的。
- **token**：
    - **說明**：這是一個 JWT (JSON Web Token) 格式的憑證。
    - **用途**：這個 token 是用來對 Pod 進行身份驗證的，Pod 使用這個 token 來向 Kubernetes API Server 發送請求，證明它的身份。

```shell
tai@tai-virtual-machine:~$ kubectl exec -it client -- sh
/omd # cd /var/run/secrets/kubernetes.io/serviceaccount
/run/secrets/kubernetes.io/serviceaccount # ls -l
total 0
lrwxrwxrwx    1 root     root            13 Jul 27 17:09 ca.crt -> ..data/ca.crt
lrwxrwxrwx    1 root     root            16 Jul 27 17:09 namespace -> ..data/namespace
lrwxrwxrwx    1 root     root            12 Jul 27 17:09 token -> ..data/token
/run/secrets/kubernetes.io/serviceaccount #
```
### 使用 ServiceAccount 進行驗證
![[K8s - ServiceAccount 1.png]]

但是此時service account並沒有存取叢集資源的權限。

![[K8s - ServiceAccount 2.png]]
### 使用 RBAC 設定權限
###### 創建 Role 和 RoleBinding：
```shell
tai@tai-virtual-machine:~$  kubectl create role demorole --verb=get,list --resource=pods
role.rbac.authorization.k8s.io/demorole created
tai@tai-virtual-machine:~$ kubectl create rolebinding demorolebinding --role=demorole --serviceaccount=default:demosa
rolebinding.rbac.authorization.k8s.io/demorolebinding created
```
###### 檢查權限：
```shell
tai@tai-virtual-machine:~$ kubectl auth can-i list pods --as=system:serviceaccount:default:demosa
yes
```
###### 再次進入 Pod 內進行測試：
```shell
tai@tai-virtual-machine:~$ kubectl exec -it client -- sh
/omd # cd /var/run/secrets/kubernetes.io/serviceaccount
/run/secrets/kubernetes.io/serviceaccount # TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/
token)
/run/secrets/kubernetes.io/serviceaccount # CACERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.cr
t
/run/secrets/kubernetes.io/serviceaccount # curl --cacert $CACERT --header "Authorization: Bearer $TOK
EN" -X GET https://kubernetes.default.svc.cluster.local/api/v1/namespaces/default/pods?limit=500
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "2688650"
  },
  ...
  ...
}
```