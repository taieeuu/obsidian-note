---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## 在 swarm 中使用 Secret
主要有兩種方式
### 從標準的收入讀取
```shell
vagrant@swarm-manager:~$ echo abc123 | docker secret create mysql_pass -
4nkx3vpdd41tbvl9qs24j7m6w
vagrant@swarm-manager:~$ docker secret ls
ID                          NAME         DRIVER    CREATED         UPDATED
4nkx3vpdd41tbvl9qs24j7m6w   mysql_pass             8 seconds ago   8 seconds ago
vagrant@swarm-manager:~$ docker secret inspect mysql_pass
[
    {
        "ID": "4nkx3vpdd41tbvl9qs24j7m6w",
        "Version": {
            "Index": 4562
        },
        "CreatedAt": "2021-07-25T22:36:51.544523646Z",
        "UpdatedAt": "2021-07-25T22:36:51.544523646Z",
        "Spec": {
            "Name": "mysql_pass",
            "Labels": {}
        }
    }
]
vagrant@swarm-manager:~$ docker secret rm mysql_pass
mysql_pass
vagrant@swarm-manager:~$
```
>[!info]
>要注意的是，terminal 是可以查看歷史紀錄的，所以要特別注意，可以清出歷史紀錄來確保密碼不外洩風險。
### 從檔案讀取
```shell
vagrant@swarm-manager:~$ ls
mysql_pass.txt
vagrant@swarm-manager:~$ more mysql_pass.txt
abc123
vagrant@swarm-manager:~$ docker secret create mysql_pass mysql_pass.txt
elsodoordd7zzpgsdlwgynq3f
vagrant@swarm-manager:~$ docker secret inspect mysql_pass
[
    {
        "ID": "elsodoordd7zzpgsdlwgynq3f",
        "Version": {
            "Index": 4564
        },
        "CreatedAt": "2021-07-25T22:38:14.143954043Z",
        "UpdatedAt": "2021-07-25T22:38:14.143954043Z",
        "Spec": {
            "Name": "mysql_pass",
            "Labels": {}
        }
    }
]
vagrant@swarm-manager:~$
```
>[!info]
>這邊是看不到 secret ，需要進到容器中的 /run/secrets/ ，可以通過`more`指令來查看內容。
### 如何使用
```shell
vagrant@swarm-manager:~$ docker service create --name mysql-demo --secret mysql_pass --env MYSQL_ROOT_PASSWORD_FILE=/run/secrets/mysql_pass mysql:5.7
wb4z2ximgqaefephu9f4109c7
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged
vagrant@swarm-manager:~$ docker service ls
ID             NAME         MODE         REPLICAS   IMAGE       PORTS
wb4z2ximgqae   mysql-demo   replicated   1/1        mysql:5.7
vagrant@swarm-manager:~$ docker service ps mysql-demo
ID             NAME           IMAGE       NODE            DESIRED STATE   CURRENT STATE            ERROR     PORTS
909429p4uovy   mysql-demo.1   mysql:5.7   swarm-worker2   Running         Running 32 seconds ago
vagrant@swarm-manager:~$
```
