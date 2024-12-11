首先初始化 flyte project，這會創建一個flyte project
```python
flytectl create project \
      --id "my-project" \
      --labels "my-label=my-project" \
      --description "My Flyte project" \
      --name "My project"
```

接著，我們可以將其推至到我們啟的 `flytectl demo start` 上，remote 就是指定在遠端執行
```python
pyflyte run --remote -p my-project -d development example.py wf --name Ada
```
附上連結：https://docs.flyte.org/en/latest/user_guide/getting_started_with_workflow_development/running_a_workflow_locally.html

