---
Date: 2024-06-23
tags:
  - devops
  - k8s
---
## nodeAffinity
使用節點上的標籤來做出調度決策`matchExpressions`
[https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/)
### requiredDuringSchedulingIgnoredDuringExecution
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
```
### PreferredDuringSchedulingIgnoredDuringExecution
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
```
## podAffinity 和 podAntiAffinity
將 Pod 調度到與其他 Pod 相同或不同的節點上
[https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)