---
Date: 2024-07-21
tags:
  - k8s
  - devops
---
>[!info]
>[https://kubernetes.io/docs/concepts/configuration/secret/](https://kubernetes.io/docs/concepts/configuration/secret/)
### 定義與用途
- Secrets 是用來儲存敏感資訊的 Kubernetes 物件，如密碼、API 令牌、密鑰和證書。
- 儲存敏感資訊時更安全且靈活。
- 這些資訊會被 base64 編碼儲存。
### Properties of Secrets
- base64 編碼
- 可以配置加密
- 儲存在 etcd 中
- 具有命名空間
- 如果引用的 Secrets 不可用，Pod 將無法啟動
### 建立 Secrets
#### 使用 `kubectl`
```shell
kubectl create secret generic mysql-secret \
  --from-literal=MYSQL_ROOT_PASSWORD=root \
  --from-literal=MYSQL_USER=demo \
  --from-literal=MYSQL_PASSWORD=demo
```
#### 使用 YAML 檔案
Secrets 的值必須是 base64 編碼

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  MYSQL_PASSWORD: ZGVtbw==
  MYSQL_ROOT_PASSWORD: cm9vdA==
  MYSQL_USER: ZGVtbw==
```

編碼/解碼

```shell
# 編碼
echo root | base64
# 輸出: cm9vdAo=

echo demo | base64
# 輸出: ZGVtbwo=

# 解碼
echo ZGVtbw== | base64 --decode
# 輸出: demo
```

從配置檔案創建

```shell
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
stringData:
  config.yaml: |
    MYSQL_ROOT_PASSWORD: root
    MYSQL_PASSWORD: demo
    MYSQL_USER: demo
```
### 在 Pods 中使用 Secrets
#### 環境變數
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql
spec:
  containers:
  - name: mysql
    image: mysql:8.0
    env:
    - name: MYSQL_ROOT_PASSWORD
      valueFrom:
        secretKeyRef:
          name: mysql-secret
          key: MYSQL_ROOT_PASSWORD
    - name: MYSQL_USER
      valueFrom:
        secretKeyRef:
          name: mysql-secret
          key: MYSQL_USER
    - name: MYSQL_PASSWORD
      valueFrom:
        secretKeyRef:
          name: mysql-secret
          key: MYSQL_PASSWORD
```

環境變數 (envFrom)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql
spec:
  containers:
  - name: mysql
    image: mysql:8.0
    envFrom:
    - secretRef:
        name: mysql-secret
```

Volumes 或 Files

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-env
spec:
  volumes:
  - name: appconfig
    secret:
      secretName: mysql-secret
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 10; done"]
    volumeMounts:
    - name: appconfig
      mountPath: "/etc/appconfig"
```
### Secrets 的類型
- 可更新 (updatable)  **默認**
- 不可變 (immutable)