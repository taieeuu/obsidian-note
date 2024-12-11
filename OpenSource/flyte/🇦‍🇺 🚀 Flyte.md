---
Date: 2024-10-19
---
### 推至 flyte docker register
首先需要有自己的image，也就是自己要撰寫dockerfile，並將其**標記推至Flyte 的內部 Registry**。

假設映像的名稱是 `my-flyte-workflow`，可以使用如下命令：

```shell
docker build -t localhost:30000/my-flyte-workflow:latest .
```
>[!info]
>代碼打包為一個 Docker 映像，並將其標記為推送到 `localhost:30000`，這是 Flyte Sandbox 中的內部 Docker Registry

**推送 Docker 映像到 Flyte 的 Docker Registry**：

```shell
docker push localhost:30000/my-flyte-workflow:latest
```
>[!info]
>這條命令會將 Docker 映像推送到 Flyte Sandbox 的 Registry，這樣 Flyte 就能夠使用這個映像來運行你的工作流。

**檢查**
```shell
curl http://localhost:30000/v2/_catalog
```

## Set Env 過程
在運行前，需要 `POD_NAMESPACE=flyte ./flyte start --config flyte-single-binary-local.yaml`

```
```shell
➜  Test pyflyte run --remote https://raw.githubusercontent.com/flyteorg/flytesnacks/master/examples/basics/basics/hello_world.py  hello_world_wf
Running Execution on Remote.

[✔] Go to http://localhost:30080/console/projects/flytesnacks/domains/development/executions/a829krxfp5nfhbc7wkbz to see execution in the console.
➜  Test
```
## 設置flytekit
在多種測試下，python=3.12，可以通過所有測試。
## flyte demo start
