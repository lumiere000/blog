---
layout: posts
title: kubernetes rke cluster deploy
image: /img/k8s.png
tags: k8s kubernetes installation ubuntu rke
category: k8s
---

install docker and kubernetes first :

```
apt-get update
apt-get install -y docker.io
(use follow if you use rancher)
curl https://releases.rancher.com/install-docker/17.03.2.sh | sh

apt-get update
apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository \
   "deb https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
   $(lsb_release -cs) \
   stable"
apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 17.03 | head -1 | awk '{print $3}')

apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubernetes-cni
```

```
swapoff -a
comment out the swap line in /etc/fstab
```


enable cgroup :

```
vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
```

add local ip for health check on each server and member :

```
vi /etc/docker/daemon.json
{
  "insecure-registries" : ["your_ip:6443"]
}
```


run on all node master and member :
```
ssh-keygen
ssh-copy-id user@node-member-ip(including server itself)
usermod -aG docker user
```

rke install :

```
curl -L https://github.com/rancher/rke/releases/download/v0.1.7/rke_linux-amd64 -o rke
chmod +x rke
mkdir /home/cluster
mv ./rke /home/cluster/rke

cd /home/cluster

vi cluster.yml

---
nodes:
  - address: 192.168.0.1
    user: user
    role: [etcd,worker,controlplane]
  - address: 192.168.0.2
    user: user
    role: [etcd,worker,controlplane]
  - address: 192.168.0.3
    user: user
    role: [etcd,worker,controlplane]

services:
  etcd:
    image: quay.io/coreos/etcd:v3.2
  kube-api:
    image: rancher/k8s:v1.9.5-rancher1-3
  kube-controller:
    image: rancher/k8s:v1.9.5-rancher1-3
  scheduler:
    image: rancher/k8s:v1.9.5-rancher1-3
  kubelet:
    image: rancher/k8s:v1.9.5-rancher1-3
  kubeproxy:
    image: rancher/k8s:v1.9.5-rancher1-3

ingress:
  provider: none
  extra_args:
    enable-ssl-passthrough: ""

network:
  plugin: calico
  options: {}
```


start building cluster :
```
./rke up
```

change the defualt config location :
```
export KUBECONFIG=/home/cluster/kube_config_cluster.yml
```

add or remove node :

1. vi /home/cluster/cluster.yml
2. add or remove node, config the role
3. save the file and run /home/cluster/rke up
#if adding a new member, please run all steps from installing k8s to config cluster.yml

failover (changing node master) :

1. take a snapshot on each server before make changes
2. change kube_config_cluster.yml server to new master ip on each node member(including master itself)
3. change the priority in /home/cluster/cluster.yml , the first active host will be the cluster master
3. run /home/cluster/rke up (this may need to run twice after the cluster.yml changed, the first time will fail healthcheck)
4. kubectl cluster-info
5. kubectl get nodes

if error appear like :
FATA[0136] [controlPlane] Failed to bring up Control Plane: Failed to verify healthcheck: Failed to check https://localhost:6443/healthz for service [kube-apiserver] on host

1. run /home/cluster/rke up again
2. make sure /etc/docker/daemon.json exist and setting is right
```
{
  "insecure-registries" : ["your_ip:6443"]
}
```
3. check /home/cluster.yml make sure etcd version is quay.io/coreos/etcd:v3.2
4. increase server's ram, cpu if the error appear again



####remark notes####

####clean up the k8s setting####
```
kubeadm reset
rm -rf /var/lib/etcd/*
```

####rke config (use if detail setting needed, )####
```
./rke config
```

fill in the rke config

####k8s initialize####
```
kubeadm init
kubeadm reset
```
