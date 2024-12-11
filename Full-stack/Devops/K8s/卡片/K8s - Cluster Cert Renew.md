---
Date: 2024-07-28
---
---
k8s集群內部通訊證書的有效期限為1年，到期後需更新證書。如果你遇到了以下問題，那麼你可能需要更新憑證：

x509: certificate has expired or is not yet valid
### 如何更新
#### 查看證書有效期限
```shell
$ sudo kubeadm certs check-expiration
[check-expiration] Reading configuration from the cluster...
[check-expiration] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Mar 04, 2025 15:35 UTC   341d            ca                      no
apiserver                  Mar 04, 2025 15:35 UTC   341d            ca                      no
apiserver-etcd-client      Mar 04, 2025 15:35 UTC   341d            etcd-ca                 no
apiserver-kubelet-client   Mar 04, 2025 15:35 UTC   341d            ca                      no
controller-manager.conf    Mar 04, 2025 15:35 UTC   341d            ca                      no
etcd-healthcheck-client    Mar 04, 2025 15:35 UTC   341d            etcd-ca                 no
etcd-peer                  Mar 04, 2025 15:35 UTC   341d            etcd-ca                 no
etcd-server                Mar 04, 2025 15:35 UTC   341d            etcd-ca                 no
front-proxy-client         Mar 04, 2025 15:35 UTC   341d            front-proxy-ca          no
scheduler.conf             Mar 04, 2025 15:35 UTC   341d            ca                      no
super-admin.conf           Mar 04, 2025 15:35 UTC   341d            ca                      no

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Mar 02, 2034 15:35 UTC   9y              no
etcd-ca                 Mar 02, 2034 15:35 UTC   9y              no
front-proxy-ca          Mar 02, 2034 15:35 UTC   9y              no
```
#### 更新證書
更新之前最好備份一下現有證書，以及etcd snapshot，以防萬一。

把這個目錄的檔案全部備份一下/etc/kubernetes/pki/

```shell
$ sudo kubeadm certs renew all
[renew] Reading configuration from the cluster...
[renew] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'

certificate embedded in the kubeconfig file for the admin to use and for kubeadm itself renewed
certificate for serving the Kubernetes API renewed
certificate the apiserver uses to access etcd renewed
certificate for the API server to connect to kubelet renewed
certificate embedded in the kubeconfig file for the controller manager to use renewed
certificate for liveness probes to healthcheck etcd renewed
certificate for etcd nodes to communicate with each other renewed
certificate for serving etcd renewed
certificate for the front proxy client renewed
certificate embedded in the kubeconfig file for the scheduler manager to use renewed
certificate embedded in the kubeconfig file for the super-admin renewed

Done renewing certificates. You must restart the kube-apiserver, kube-controller-manager, kube-scheduler and etcd, so that they can use the new certificates.
```

執行完之後，需要**重新啟動**kube-apiserver, kube-controller-manager, kube-scheduler和etcd。

暴力方法就是直接重啟叢集的所有節點。比較溫和的方法是依照我們前面講的叢集upgrade的方法，依序重啟叢集的所有節點，重啟節點之前需進行節點的drain操作。