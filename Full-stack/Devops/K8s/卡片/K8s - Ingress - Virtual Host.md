---
Date: 2024-07-27
---
---
![[K8s - Ingress - Virtual Host 1.png]]

繼承上一篇[[K8s - Exposing Multiple Services with Ingress (Simple fanout )]]
### 創建 Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-multiple
spec:
  ingressClassName: nginx
  rules:
    - host: v1.api.example.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: web1
              port:
                number: 9001
    - host: v2.api.example.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: web2
              port:
                number: 9002
```

保存，應用:

```shell
tai@tai-virtual-machine:~$ kubectl apply -f ingress-virtual-host.yaml
```
### 設置 `/etc/hosts` 文件
1. 設置 `v1.api.example.com`

```shell
10.108.109.142 v1.api.example.com
```

2. 設置 `v2.api.example.com`

```shell
10.108.109.142 v2.api.example.com
```
### 驗證
**`v1.api.example.com`**
```shell
tai@tai-virtual-machine:~$ curl v1.api.example.com
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-56gq7
tai@tai-virtual-machine:~$ curl v1.api.example.com
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-m2h24
tai@tai-virtual-machine:~$ curl v1.api.example.com
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-56gq7
```

**`v2.api.example.com`**
```shell
tai@tai-virtual-machine:~$ curl v2.api.example.com
Hello, world!
Version: 2.0.0
Hostname: web2-7bb99d547c-dkppl
tai@tai-virtual-machine:~$ curl v2.api.example.com
Hello, world!
Version: 2.0.0
Hostname: web2-7bb99d547c-bsq8m
```