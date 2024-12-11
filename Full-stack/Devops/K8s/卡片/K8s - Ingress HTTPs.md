---
Date: 2024-07-27
---
---
繼承 [[K8s - Ingress - Virtual Host]]
### 生成 TLS 密鑰和證書
使用 OpenSSL 生成自簽名的 TLS 密鑰和證書，這會生成 `tls.key` 和 `tls.crt` 文件。

```shell
tai@tai-virtual-machine:~$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=api.example.com"
```
### 創建 Kubernetes Secret
使用生成的密鑰和證書創建 Kubernetes Secret：

```shell
tai@tai-virtual-machine:~$ kubectl create secret tls test-tls --key="tls.key" --cert="tls.crt"
secret/test-tls created
tai@tai-virtual-machine:~$
```

檢查已創建的 Secret：

```shell
tai@tai-virtual-machine:~$ kubectl get secrets
NAME       TYPE                DATA   AGE
test-tls   kubernetes.io/tls   2      26s
tai@tai-virtual-machine:~$
```
### 創建 Ingress 資源
創建一個使用 TLS 的 Ingress 資源配置文件 `ingress-https.yaml`：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-https
spec:
  ingressClassName: nginx
  tls:
  - hosts:
      - api.example.com
    secretName: test-tls
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web1
            port:
              number: 9001
```

應用它，並記得要修改 `/etc/hosts` 
### 驗證
```shell
tai@tai-virtual-machine:~$ curl api.example.com
<html>
<head><title>308 Permanent Redirect</title></head>
<body>
<center><h1>308 Permanent Redirect</h1></center>
<hr><center>nginx</center>
</body>
</html>
tai@tai-virtual-machine:~$ curl https://api.example.com
curl: (60) SSL certificate problem: self-signed certificate
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
tai@tai-virtual-machine:~$ curl https://api.example.com --insecure
Hello, world!
Version: 1.0.0
Hostname: web1-8557d5bd5c-56gq7
tai@tai-virtual-machine:~$
```