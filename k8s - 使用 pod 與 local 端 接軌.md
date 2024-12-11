---
Date: 2024-10-21
---
在 k8s 世界中，許多資源與伺服器都是運行在內部的，並不能做從我們本地機器進行訪問，但我們可以利用 `kubectl port-fordward`，將 k8s 中的服務連接埠映射到本地連接埠。

```shell
kubectl port-forward TYPE/NAME [option] [LOCAL_PORT: ]REMOTE_PORT

kubectl port-forward pod/foo 8080:8080
```