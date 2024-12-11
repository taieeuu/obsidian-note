---
Date: 2024-06-04
---
## Swarm stack 佈署多 service 應用
前面說明展示的，都是手動去佈署一個一個去創建，但是我們知道透過 docker-compose 可以去定義多個 service 以及他們之間的關聯，我們可以定義一組 service 透過 docker-compose 去一個在單節點上環境進行快速佈署，在 swarm 中我們也可以使用  docker-compose 文件來進行，只不過不是使用 docker-compose 命令，而是使用 stack 這個命令。

實驗代碼: 
```shell
vagrant@swarm-manager:~$ git clone https://github.com/xiaopeng163/flask-redis
Cloning into 'flask-redis'...
remote: Enumerating objects: 22, done.
remote: Counting objects: 100% (22/22), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 22 (delta 9), reused 7 (delta 2), pack-reused 0
Unpacking objects: 100% (22/22), 8.60 KiB | 1.07 MiB/s, done.
vagrant@swarm-manager:~$ cd flask-redis
vagrant@swarm-manager:~/flask-redis$ ls
Dockerfile  LICENSE  README.md  app.py  docker-compose.yml
vagrant@swarm-manager:~/flask-redis$
```

環境清理
```shell
vagrant@swarm-manager:~/flask-redis$ docker system prune -a -f
```

鏡像建置和提交，如果你嘗試這一步，可以把 docker-compose.yml 裡的 `xiaopeng163/flask-redis`改成你的 dockerhub id
```shell
vagrant@swarm-manager:~/flask-redis$ docker-compose build
vagrant@swarm-manager:~/flask-redis$ docker image ls
REPOSITORY                TAG          IMAGE ID       CREATED         SIZE
xiaopeng163/flask-redis   latest       5efb4fcbcfc3   6 seconds ago   126MB
python                    3.9.5-slim   c71955050276   3 weeks ago     115MB
```

提交鏡像到 dockerhub
```shell
vagrant@swarm-manager:~/flask-redis$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xiaopeng163
Password:
WARNING! Your password will be stored unencrypted in /home/vagrant/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
vagrant@swarm-manager:~/flask-redis$ docker-compose push
WARNING: The REDIS_PASSWORD variable is not set. Defaulting to a blank string.
Pushing flask (xiaopeng163/flask-redis:latest)...
The push refers to repository [docker.io/xiaopeng163/flask-redis]
f447d33c161b: Pushed
f7395da2fd9c: Pushed
5b156295b5a3: Layer already exists
115e0863702d: Layer already exists
e10857b94a57: Layer already exists
8d418cbfaf25: Layer already exists
764055ebc9a7: Layer already exists
latest: digest: sha256:c909100fda2f4160b593b4e0fb692b89046cebb909ae90546627deca9827b676 size: 1788
vagrant@swarm-manager:~/flask-redis$
```

透過stack啟動服務
```shell
vagrant@swarm-manager:~/flask-redis$ env REDIS_PASSWORD=ABC123 docker stack deploy --compose-file docker-compose.yml flask-demo
Ignoring unsupported options: build

Creating network flask-demo_default
Creating service flask-demo_flask
Creating service flask-demo_redis-server
vagrant@swarm-manager:~/flask-redis$
vagrant@swarm-manager:~/flask-redis$ docker stack ls
NAME         SERVICES   ORCHESTRATOR
flask-demo   2          Swarm
vagrant@swarm-manager:~/flask-redis$ docker stack ps flask-demo
ID             NAME                        IMAGE                            NODE            DESIRED STATE   CURRENT STATE
ERROR     PORTS
lzm6i9inoa8e   flask-demo_flask.1          xiaopeng163/flask-redis:latest   swarm-manager   Running         Running 23 seconds ago

ejojb0o5lbu0   flask-demo_redis-server.1   redis:latest                     swarm-worker2   Running         Running 21 seconds ago

vagrant@swarm-manager:~/flask-redis$ docker stack services flask-demo
ID             NAME                      MODE         REPLICAS   IMAGE                            PORTS
mpx75z1rrlwn   flask-demo_flask          replicated   1/1        xiaopeng163/flask-redis:latest   *:8080->5000/tcp
z85n16zsldr1   flask-demo_redis-server   replicated   1/1        redis:latest
vagrant@swarm-manager:~/flask-redis$ docker service ls
ID             NAME                      MODE         REPLICAS   IMAGE                            PORTS
mpx75z1rrlwn   flask-demo_flask          replicated   1/1        xiaopeng163/flask-redis:latest   *:8080->5000/tcp
z85n16zsldr1   flask-demo_redis-server   replicated   1/1        redis:latest
vagrant@swarm-manager:~/flask-redis$ curl 127.0.0.1:8080
Hello Container World! I have been seen 1 times and my hostname is 21d63a8bfb57.
vagrant@swarm-manager:~/flask-redis$ curl 127.0.0.1:8080
Hello Container World! I have been seen 2 times and my hostname is 21d63a8bfb57.
vagrant@swarm-manager:~/flask-redis$ curl 127.0.0.1:8080
Hello Container World! I have been seen 3 times and my hostname is 21d63a8bfb57.
vagrant@swarm-manager:~/flask-redis$
```
>[!info]
>如果 swarm 沒有指定 網絡，則會自動預設 overlay 網路，但如果使用相同的 docker-compose文件，則會預設 bridge 網路

**在coding 時，盡量把docker-compose.yml 切分環境，像是開發: docker-compse-dev.yml 正式: docker-compose-prod.yml**