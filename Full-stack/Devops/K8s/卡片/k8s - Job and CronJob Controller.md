---
Date: 2024-06-23
tags:
  - devops
  - k8s
---
## Job
一次性運行的Pod，一般為執行某個指令或腳本，執行結束後pod的生命隨之結束。

create job
```shell
$ kubectl create job my-job --image=busybox -- sh -c "sleep 50"
```

基本 yml 配置
```yml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: my-job
        image: busybox
        command: ["sh",  "-c", "sleep 50"]
      restartPolicy: Never
```

```shell
$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
my-job-z679f   1/1     Running   0          10s

$ kubectl get pods
NAME           READY   STATUS      RESTARTS   AGE
my-job-z679f   0/1     Completed   0          63s
```
## CronJob
計劃任務

create CronJob
```yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

![[Pasted image 20240623005833.png]]
>[!info]
>`cronjobs.batch` 是指 `CronJob` 資源的 API 群組和版本。
>`-o wide` 是一個選項，用來以寬格式顯示資源的詳細信息
### 列出 cronjob
```
kubectl get cronjob
```
### 刪除 cronjob
```shell
kubectl delete cronjob hello-cronjob

or 

kubectl delete pod -l job-name=hello #所有 cronjob
```