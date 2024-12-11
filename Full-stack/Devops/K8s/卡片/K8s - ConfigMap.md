---
Date: 2024-07-21
tags:
  - k8s
  - devops
---
### 定義與用途
- ConfigMaps 用來將鍵值對暴露到 Pod 中，用於應用程式配置設定。
- 將應用程式與 Pod 配置解耦。
### 建立 ConfigMaps
#### 使用 `kubectl`
```shell
kubectl create configmap mysql-cfg \
  --from-literal=MYSQL_ROOT_PASSWORD=root \
  --from-literal=MYSQL_USER=demo \
  --from-literal=MYSQL_PASSWORD=demo
```
#### 從檔案建立
```shell
kubectl create configmap mysql-cfg --from-file=appconfig
```
#### 使用 YAML 檔案
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: appconfig
data:
  MYSQL_ROOT_PASSWORD: root
  MYSQL_USER: demo
  MYSQL_PASSWORD: demo
```
### 在 Pods 中使用 ConfigMaps
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
        configMapKeyRef:
            name: mysql-secret
            key: MYSQL_ROOT_PASSWORD
    - name: MYSQL_USER
      valueFrom:
        configMapKeyRef:
            name: mysql-secret
            key: MYSQL_USER
    - name: MYSQL_PASSWORD
      valueFrom:
        configMapKeyRef:
            name: mysql-secret
            key: MYSQL_PASSWORD
```
#### 環境變數 (envFrom)
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
      - configMapRef:
          name: appconfig
```
#### Volumes
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-env
spec:
  volumes:
  - name: appconfig
    configMap:
      name: appconfig
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 10; done"]
    volumeMounts:
    - name: appconfig
      mountPath: "/etc/appconfig"
```