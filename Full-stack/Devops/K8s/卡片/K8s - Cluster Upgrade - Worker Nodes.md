---
Date: 2024-07-28
---
---
### Upgrade work node
```shell
# go to master node
kubectl drain k8s-worker1 --ingore-daemonsets

# update kubeadm
sudo apt-mark unhold kubeadm
sudo apt-get update
sudo apt-get install -y kubeadm=$TARGET_VERSION
sudo apt-mark hold kubeadm

sudo kubeadm upgrade node

# update kubelet and kubectl
sudo apt-mark unhold kubelet
sudo apt-get update
sudo apt-get install -y kubelet=$TARGET_VERSION
sudo apt-mark hold kubelet

# go to master node, uncordon this node
kubectl uncordon k8s-worker1
```

