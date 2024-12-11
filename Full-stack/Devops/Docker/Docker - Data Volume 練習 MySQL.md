---
Date: 2024-06-09
tags:
  - docker
  - devops
---
## Data Volume 練習MySQL
### 準備 image
```shell
$ docker pull mysql:5.7
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
mysql        5.7       2c9028880e58   5 weeks ago    447MB
```
### 構建容器
```shell
$ docker container run --name some-mysql -e MYSQL_ROOT_PASSWORD=123 -d -v mysql-data:/var/lib/mysql mysql:5.7
02206eb369be08f660bf86b9d5be480e24bb6684c8a938627ebfbcfc0fd9e48e
$ docker volume ls
DRIVER    VOLUME NAME
local     mysql-data
$ docker volume inspect mysql-data
[
    {
        "CreatedAt": "2021-06-21T23:55:23+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data",
        "Name": "mysql-data",
        "Options": null,
        "Scope": "local"
    }
]
$
```
### 資料庫寫入資料
進入MySQL的shell，密碼是`123`
```shell
$ docker container exec -it 022 sh
# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.34 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> create database demo;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| demo               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> exit
Bye
# exit
```

建立了一個叫demo的資料庫

查看data volume
```shell
$ docker volume inspect mysql-data
[
    {
        "CreatedAt": "2021-06-22T00:01:34+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data",
        "Name": "mysql-data",
        "Options": null,
        "Scope": "local"
    }
]
$ ls  /var/lib/docker/volumes/mysql-data/_data
auto.cnf    client-cert.pem  ib_buffer_pool  ibdata1  performance_schema  server-cert.pem
ca-key.pem  client-key.pem   ib_logfile0     ibtmp1   private_key.pem     server-key.pem
ca.pem      demo             ib_logfile1     mysql    public_key.pem      sys
$
```