---
Date: 2024-07-21
tags:
  - k8s
  - devops
---
在 Kubernetes 中，可以使用多種方式在 Pod 中配置應用程式。
#### 配置應用程式方式
1. **命令列參數 (Command Line Arguments)**
2. **環境變數 (Environment Variables)**
3. **ConfigMaps**
### 環境變數 (Environment Variables) 內部使用
- User define
- System defined
#### 使用者定義的環境變數 (User Defined Environment Variables)
使用者可以在 Pod 的 YAML 配置檔中設定環境變數。這些環境變數可以是靜態值，也可以從 ConfigMap 或 Secret 中導入。
##### 在 YAML 配置檔中設定靜態環境變數：
**deployment.yml**
```yml
apiVersion: v1
kind: Pod
metadata:
  name: pod-env
spec:
  containers:
  - name: producer
    image: busybox
    command: ["sh", "-c", "while true; do echo $NAME >> /tmp/index.html; sleep 10; done"]
    env:
    - name: NAME
      value: Hello World
```

apply and check the file

```shell
tai@tai-virtual-machine:~$ kubectl apply -f deployment.yml
pod/pod-env created
tai@tai-virtual-machine:~$ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
pod-env   1/1     Running   0          18s
tai@tai-virtual-machine:~$  kubectl exec pod-env -- more /tmp/index.html
Hello World
Hello World
Hello World
tai@tai-virtual-machine:~$
```

Another example : MySQL containers

```yml
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
      value: root
```