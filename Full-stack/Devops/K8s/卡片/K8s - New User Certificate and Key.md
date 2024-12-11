---
Date: 2024-07-27
---
---
### 建立新用戶證書
**使用 OpenSSL 建立私鑰**：
此時會產生出 一個 2048 位元的 RSA 私鑰，並將其儲存到 `demouser.key` 文件中。

```shell
openssl genrsa -out demouser.key 2048
```

**使用 OpenSSL 生成證書簽名請求 (Certificate Signing Request, CSR)**：
- CN（Common Name）是你的用戶名，O（Organization）是群組。

根據 `demouser.key` 私鑰生成一個證書簽名請求 (Certificate Signing Request, CSR)，並將其儲存到 `demouser.csr` 文件中

```shell
tai@tai-virtual-machine:~$ openssl req -new -key demouser.key -out demouser.csr -subj "/CN=demouser"
```

**進行簽名，需將CSR進行Base64編碼，與建立 CertificateSigningRequest 對象:**
當然這邊我們也可以使用 `openssl` 進行，但我們這邊使用 `k8s` 提供的內置 `apiserver` 進行簽名。

```shell
cat demouser.csr | base64 | tr -d "\n"
```

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: demouser
spec:
  groups:
  - system:authenticated
  request: <將 base64 編碼的 csr 放在這裡>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

**批准 CertificateSigningRequest 對象**：
可以看到 `demouser` 的 CSR 狀態是 `Pending`。這表示該請求還沒有被批准。為了讓 CSR 狀態從 `Pending` 變為 `Approved`。

![[K8s - New User Certificate and Key 1.png]]
```shell
tai@tai-virtual-machine:~$ kubectl certificate approve demouser
certificatesigningrequest.certificates.k8s.io/demouser approved
tai@tai-virtual-machine:~$ kubectl get certificatesigningrequests.certificates.k8s.io
NAME       AGE     SIGNERNAME                            REQUESTOR          REQUESTEDDURATION   CONDITION
demouser   3m14s   kubernetes.io/kube-apiserver-client   kubernetes-admin   <none>              Approved,Issued
```

**檢查證書**：

```shell
openssl x509 -in demouser.crt -text
```





