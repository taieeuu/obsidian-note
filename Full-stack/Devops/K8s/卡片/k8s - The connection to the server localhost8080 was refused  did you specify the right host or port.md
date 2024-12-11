---
Date: 2024-06-16
tags:
  - devops
  - k8s
---
表明 `kubectl` 正在嘗試連接本地的 API 伺服器（localhost:8080），但該伺服器不可用。這可能是由於 `kubectl` 配置文件不正確或 `kubelet` 沒有正確配置 `kubeconfig`。
## 設置 `kubeconfig` 文件
以下是具體步驟：
1. **從控制平面節點複製 `kubeconfig` 文件**
2. **設置 `KUBECONFIG` 環境變量**
## 步驟 1：從控制平面節點複製 `kubeconfig` 文件
假設你在控制平面節點 `tai-virtual-machine` 上擁有 `admin.conf` 文件，可以將其複製到你的工作節點。
#### 在控制平面節點上：

首先，將 `admin.conf` 複製到一個你有權限訪問的目錄，例如你的 home 目錄，並設置適當的權限：

```shell
sudo cp /etc/kubernetes/admin.conf /home/tai/
sudo chown tai: /home/tai/admin.conf
```

#### 在工作節點上：

然後在工作節點上使用 `scp` 命令將這個文件複製到本地：
```shell
scp tai@tai-virtual-machine:/home/tai/admin.conf ~/.kube/config
```
## 步驟 2：設置 `KUBECONFIG` 環境變量
在工作節點上設置 `KUBECONFIG` 環境變量，使 `kubectl` 使用這個配置文件：
```shell
export KUBECONFIG=~/.kube/config
```

你也可以將這一行添加到你的 shell 配置文件（如 `~/.bashrc` 或 `~/.zshrc`），以便在每次登錄時自動設置：
```shell
echo "export KUBECONFIG=~/.kube/config" >> ~/.bashrc source ~/.bashrc
```
### 驗證配置
設置完成後，嘗試運行 `kubectl` 命令來驗證配置：
```shell
kubectl get nodes
```