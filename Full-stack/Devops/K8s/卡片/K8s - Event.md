---
Date: 2024-07-30
---
---
### Event global
獲取所有事件：

```shell
kubectl get events
```

獲取特定條件的事件，例如類型為Warning且原因為Failed的事件：

```shell
kubectl get events --field-selector type=Warning,reason=Failed
```

實時監控事件（可以使用 `fg` 和 `ctrl + c` 來中斷監控）

```shell
kubectl get events --watch &
```
### Event per resource
使用 `kubectl describe` 來查看特定資源的詳細信息，包括事件。

查看特定Pod的詳細信息：
```shell
kubectl describe pods nginx
```