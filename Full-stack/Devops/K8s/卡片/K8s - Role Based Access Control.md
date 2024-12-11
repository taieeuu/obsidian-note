---
Date: 2024-07-28
---
---
繼承上一篇[[K8s - Update kubeconig - 添加用户和context]]
### Role Based Access Control (RBAC)
RBAC 用於控制 Kubernetes 集群中用戶的訪問權限。主要包含以下 API 對象：

- **Role** 和 **ClusterRole**：定義可以對 Kubernetes 資源執行的操作。
- **RoleBinding** 和 **ClusterRoleBinding**：定義誰可以執行 Role 或 ClusterRole 中定義的操作。
### Roles
Roles 用於在特定 namespace 中定義對 Kubernetes 資源的操作權限。

**創建 Role**：
`*` 代表權線全開的意思

```shell
$ kubectl create role demorole --verb=get,list --resource=pods --namespace ns1
$ kubectl create role demorole --verb=* --resource=pods --namespace ns1
```
### ### ClusterRoles
ClusterRoles 用於跨 namespace 定義對集群級別資源（如 Node、PersistentVolumes）的操作權限。

**創建 ClusterRole**：

```shell
$ kubectl create clusterrole democlusterrole --verb=get,list --resource=nodes
```
### RoleBinding 和 ClusterRoleBinding
- **RoleBinding**：將 Role 綁定到特定用戶，使其可以在指定 namespace 中執行 Role 定義的操作。
- **ClusterRoleBinding**：將 ClusterRole 綁定到特定用戶，使其可以跨 namespace 執行 ClusterRole 定義的操作。
#### RoleBinding
**創建 RoleBinding**：

```shell
$ kubectl create rolebinding demorolebinding --role=demorole --user=demouser --namespace=ns1
```
#### ClusterRoleBinding
**創建 ClusterRoleBinding**：

```shell
$ kubectl create clusterrolebinding democlusterrolebinding --clusterrole=democlusterrole --user=demouser
```
### 使用場景
- **Role/RoleBinding**：一般用於單個 namespace 定義權限。
- **ClusterRole/ClusterRoleBinding**：一般用於跨 namespace 定義權限。
### 測試
#### Role 和 RoleBinding 測試
###### 以管理員身份創建一些資源：
![[K8s - Role Based Access Control 1.png]]
###### 測試權限：
```shell
tai@tai-virtual-machine:~$ kubectl auth can-i list pod
yes
tai@tai-virtual-machine:~$ kubectl auth can-i list pod --as demouser
no
tai@tai-virtual-machine:~$
```
###### 創建 Role 和 RoleBinding：
```shell
tai@tai-virtual-machine:~$ kubectl create role demorole --verb=get,list --resource=pods --namespace ns1
role.rbac.authorization.k8s.io/demorole created
tai@tai-virtual-machine:~$ kubectl create rolebinding demorolebinding --role=demorole --user=demouser --namespace ns1
rolebinding.rbac.authorization.k8s.io/demorolebinding created
tai@tai-virtual-machine:~$
```
###### 再次測試權限：
```shell
tai@tai-virtual-machine:~$  kubectl auth can-i list node
Warning: resource 'nodes' is not namespace scoped

yes
tai@tai-virtual-machine:~$ kubectl auth can-i list node --as demouser
Warning: resource 'nodes' is not namespace scoped

yes
```