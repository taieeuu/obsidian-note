---
Date: 2024-06-26
tags:
  - devops
  - k8s
---
## NFS Server Setup
```shell
# install NFS server and create directory for our exports

sudo apt-get install -y nfs-kernel-server
sudo mkdir -p /export/volumes
sudo mkdir -p /export/volumes/pod

# config NFS export

sudo bash -c 'echo "/export/volumes *(rw,no_root_squash,no_subtree_check)" > /etc/exports'
cat /etc/exports
sudo systemctl restart nfs-kernel-server.service
```
## NFS client test
>[!Warning]
>注意，需要在Kubernetes集群的所有节点上安装NFS Client

```shell
$ sudo apt-get install -y nfs-common
```

```shell
$ sudo mount -t nfs nfs-server-address:/export/volumes /mnt/
$ mount | grep nfs
$ sudo umount /mnt
```