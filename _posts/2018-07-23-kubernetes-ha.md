---
layout: posts
title: kubernetes ha cluster deploy
image: /img/k8s.png
tags: k8s kubernetes installation ubuntu kubeadm
category: k8s
---

### kurbernetes 3 master etcd and also worker HA ready example

infrastructure :
```
VIP of keepalived (loadblancer ip) : 192.168.0.100
k8s-node1 : 192.168.0.101 roles : etcd master worker haproxy keepalived
k8s-node2 : 192.168.0.102 roles : etcd master worker haproxy keepalived
k8s-node3 : 192.168.0.103 roles : etcd master worker haproxy keepalived
```

add hostname on every nodes :
```
cat >>/etc/hosts<<EOF
192.168.0.101 k8s-node1
192.168.0.102 k8s-node2
192.168.0.103 k8s-node3
EOF
```

install docker and kubernetes first :

kubernetes support docker version : 1.12.6 or 1.13.1 or 17.03.2


remove current docker (if necessarily)
```
sudo apt-get remove docker docker-engine docker-ce docker.io
```

docker install :
```
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
```

kubernetes install :
```
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubernetes-cni
```

stop swap :
```
swapoff -a
```
comment out the swap line in /etc/fstab

enable cgroup :
```
vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
```

flannel prepare :
```
mkdir -p /etc/cni/net.d
vi /etc/cni/net.d/10-flannel.conflist

{
  "name": "cbr0",
  "plugins": [
    {
      "type": "flannel",
      "delegate": {
        "hairpinMode": true,
        "isDefaultGateway": true
      }
    },
    {
      "type": "portmap",
      "capabilities": {
        "portMappings": true
      }
    }
  ]
}
```

del old cert on other node before :
```
rm /etc/kubernetes/kubelet.conf
rm /etc/kubernetes/admin.conf
```

haproxy :
```
docker pull haproxy:1.7.8-alpine
mkdir /etc/haproxy
cat >/etc/haproxy/haproxy.cfg<<EOF
global
  log 127.0.0.1 local0 err
  maxconn 50000
  uid 99
  gid 99
  #daemon
  nbproc 1
  pidfile haproxy.pid

defaults
  mode http
  log 127.0.0.1 local0 err
  maxconn 50000
  retries 3
  timeout connect 5s
  timeout client 30s
  timeout server 30s
  timeout check 2s

listen admin_stats
  mode http
  bind 0.0.0.0:1080
  log 127.0.0.1 local0 err
  stats refresh 30s
  stats uri     /haproxy-status
  stats realm   Haproxy\ Statistics
  stats auth    admin:admin
  stats hide-version
  stats admin if TRUE

frontend k8s-https
  bind 0.0.0.0:8443
  mode tcp
  #maxconn 50000
  default_backend k8s-https

backend k8s-https
  mode tcp
  balance roundrobin
  server k8s-node1 192.168.0.101:6443 weight 1 maxconn 1000 check inter 2000 rise 2 fall 3
  server k8s-node2 192.168.0.102:6443 weight 1 maxconn 1000 check inter 2000 rise 2 fall 3
  server k8s-node3 192.168.0.103:6443 weight 1 maxconn 1000 check inter 2000 rise 2 fall 3
EOF
```

```
docker run -d --name my-haproxy \
-v /etc/haproxy:/usr/local/etc/haproxy:ro \
-p 8443:8443 \
-p 1080:1080 \
--restart always \
haproxy:1.7.8-alpine
```

keepalive :
```
docker run --net=host --cap-add=NET_ADMIN \
-e KEEPALIVED_INTERFACE=ens160 \
-e KEEPALIVED_VIRTUAL_IPS="#PYTHON2BASH:['192.168.0.100']" \
-e KEEPALIVED_UNICAST_PEERS="#PYTHON2BASH:['192.168.0.101','192.168.0.102','192.168.0.103']" \
-e KEEPALIVED_PASSWORD=hello \
--name k8s-keepalived \
--restart always \
-d osixia/keepalived:1.4.4
```

vi /etc/systemd/system/kubelet.service.d/10-kubeadm.conf !!!replace your node ip at the end :
```
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_SYSTEM_PODS_ARGS=--pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true"
Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin"
Environment="KUBELET_DNS_ARGS=--cluster-dns=10.96.0.10 --cluster-domain=cluster.local"
Environment="KUBELET_AUTHZ_ARGS=--authorization-mode=Webhook --client-ca-file=/etc/kubernetes/pki/ca.crt"
Environment="KUBELET_CADVISOR_ARGS=--cadvisor-port=0"
Environment="KUBELET_CERTIFICATE_ARGS=--rotate-certificates=true --cert-dir=/var/lib/kubelet/pki"
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CERTIFICATE_ARGS $KUBELET_EXTRA_ARGS  --node-ip=192.168.x.x
```

allow communication between containers :
```
iptables -P FORWARD ACCEPT
```

build master (node1) :

```
CP0_IP="192.168.0.100"
CP0_HOSTNAME="k8s-node1"
cat >kubeadm-master.config<<EOF
apiVersion: kubeadm.k8s.io/v1alpha2
kind: MasterConfiguration
kubernetesVersion: v1.11.0

apiServerCertSANs:
- "k8s-node1"
- "k8s-node2"
- "k8s-node3"
- "192.168.0.101"
- "192.168.0.102"
- "192.168.0.103"
- "192.168.0.100"
- "127.0.0.1"

api:
  advertiseAddress: $CP0_IP
  controlPlaneEndpoint: 192.168.0.100:8443

etcd:
  local:
    extraArgs:
      listen-client-urls: "https://127.0.0.1:2379,https://$CP0_IP:2379"
      advertise-client-urls: "https://$CP0_IP:2379"
      listen-peer-urls: "https://$CP0_IP:2380"
      initial-advertise-peer-urls: "https://$CP0_IP:2380"
      initial-cluster: "$CP0_HOSTNAME=https://$CP0_IP:2380"
    serverCertSANs:
      - $CP0_HOSTNAME
      - $CP0_IP
    peerCertSANs:
      - $CP0_HOSTNAME
      - $CP0_IP

controllerManagerExtraArgs:
  node-monitor-grace-period: 10s
  pod-eviction-timeout: 10s

networking:
  podSubnet: 10.244.0.0/16

kubeProxy:
  config:
    # mode: ipvs
    mode: iptables
EOF
```

init and build master (node1) :
```
kubeadm init --config kubeadm-master.config
```

after master built :
```
mkdir ~/.kube/
cp /etc/kubernetes/admin.conf ~/.kube/config
```

cp cert to node2 and node3 :
```
cd /etc/kubernetes && tar cvzf k8s-key.tgz admin.conf pki/ca.* pki/sa.* pki/front-proxy-ca.* pki/etcd/ca.*
scp k8s-key.tgz user@k8s-node2:/tmp/
scp k8s-key.tgz user@k8s-node3:/tmp/
ssh user@k8s-node1
sudo tar xf /tmp/k8s-key.tgz -C /etc/kubernetes/
ssh user@k8s-node2
sudo tar xf /tmp/k8s-key.tgz -C /etc/kubernetes/
```

node2 :
```
CP0_IP="192.168.0.101"
CP0_HOSTNAME="k8s-node1"
CP1_IP="192.168.0.102"
CP1_HOSTNAME="k8s-node2"
cat >kubeadm-master.config<<EOF
apiVersion: kubeadm.k8s.io/v1alpha2
kind: MasterConfiguration
kubernetesVersion: v1.11.0

apiServerCertSANs:
- "k8s-node1"
- "k8s-node2"
- "k8s-node3"
- "192.168.0.101"
- "192.168.0.102"
- "192.168.0.103"
- "192.168.0.100"
- "127.0.0.1"

api:
  advertiseAddress: 192.168.0.102
  controlPlaneEndpoint: 192.168.0.100:8443

etcd:
  local:
    extraArgs:
      listen-client-urls: "https://127.0.0.1:2379,https://$CP1_IP:2379"
      advertise-client-urls: "https://$CP1_IP:2379"
      listen-peer-urls: "https://$CP1_IP:2380"
      initial-advertise-peer-urls: "https://$CP1_IP:2380"
      initial-cluster: "$CP0_HOSTNAME=https://$CP0_IP:2380,$CP1_HOSTNAME=https://$CP1_IP:2380"
      initial-cluster-state: existing
    serverCertSANs:
      - $CP1_HOSTNAME
      - $CP1_IP
    peerCertSANs:
      - $CP1_HOSTNAME
      - $CP1_IP

controllerManagerExtraArgs:
  node-monitor-grace-period: 10s
  pod-eviction-timeout: 10s

networking:
  podSubnet: 10.244.0.0/16

kubeProxy:
  config:
    # mode: ipvs
    mode: iptables
EOF
```

set kubelet :
```
kubeadm alpha phase certs all --config kubeadm-master.config
kubeadm alpha phase kubelet config write-to-disk --config kubeadm-master.config
kubeadm alpha phase kubelet write-env-file --config kubeadm-master.config
kubeadm alpha phase kubeconfig kubelet --config kubeadm-master.config
systemctl daemon-reload
systemctl restart kubelet
```

add node to etcd cluster:
```
CP0_IP="192.168.0.101"
CP0_HOSTNAME="k8s-node1"
CP1_IP="192.168.0.102"
CP1_HOSTNAME="k8s-node2"
KUBECONFIG=/etc/kubernetes/admin.conf kubectl exec -n kube-system etcd-${CP0_HOSTNAME} -- etcdctl --ca-file /etc/kubernetes/pki/etcd/ca.crt --cert-file /etc/kubernetes/pki/etcd/peer.crt --key-file /etc/kubernetes/pki/etcd/peer.key --endpoints=https://${CP0_IP}:2379 member add ${CP1_HOSTNAME} https://${CP1_IP}:2380
kubeadm alpha phase etcd local --config kubeadm-master.config
```

add node2 to k8s cluster and set role :
```
kubeadm alpha phase kubeconfig all --config kubeadm-master.config
kubeadm alpha phase controlplane all --config kubeadm-master.config
kubeadm alpha phase mark-master --config kubeadm-master.config
```

node3 :
```
CP0_IP="192.168.0.101"
CP0_HOSTNAME="k8s-node1"
CP1_IP="192.168.0.102"
CP1_HOSTNAME="k8s-node2"
CP2_IP="192.168.0.103"
CP2_HOSTNAME="k8s-node3"
cat >kubeadm-master.config<<EOF
apiVersion: kubeadm.k8s.io/v1alpha2
kind: MasterConfiguration
kubernetesVersion: v1.11.0

apiServerCertSANs:
- "k8s-node1"
- "k8s-node2"
- "k8s-node3"
- "192.168.0.101"
- "192.168.0.102"
- "192.168.0.103"
- "192.168.0.100"
- "127.0.0.1"


api:
  advertiseAddress: $CP2_IP
  controlPlaneEndpoint: 192.168.0.100:8443

etcd:
  local:
    extraArgs:
      listen-client-urls: "https://127.0.0.1:2379,https://$CP2_IP:2379"
      advertise-client-urls: "https://$CP2_IP:2379"
      listen-peer-urls: "https://$CP2_IP:2380"
      initial-advertise-peer-urls: "https://$CP2_IP:2380"
      initial-cluster: "$CP0_HOSTNAME=https://$CP0_IP:2380,$CP1_HOSTNAME=https://$CP1_IP:2380,$CP2_HOSTNAME=https://$CP2_IP:2380"
      initial-cluster-state: existing
    serverCertSANs:
      - $CP2_HOSTNAME
      - $CP2_IP
    peerCertSANs:
      - $CP2_HOSTNAME
      - $CP2_IP

controllerManagerExtraArgs:
  node-monitor-grace-period: 10s
  pod-eviction-timeout: 10s

networking:
  podSubnet: 10.244.0.0/16

kubeProxy:
  config:
    # mode: ipvs
    mode: iptables
EOF
```

set kubelet :
```
kubeadm alpha phase certs all --config kubeadm-master.config
kubeadm alpha phase kubelet config write-to-disk --config kubeadm-master.config
kubeadm alpha phase kubelet write-env-file --config kubeadm-master.config
kubeadm alpha phase kubeconfig kubelet --config kubeadm-master.config
systemctl restart kubelet
```

add to etcd :
```
CP0_IP="192.168.0.101"
CP0_HOSTNAME="k8s-node1"
CP2_IP="192.168.0.103"
CP2_HOSTNAME="k8s-node3"
KUBECONFIG=/etc/kubernetes/admin.conf kubectl exec -n kube-system etcd-${CP0_HOSTNAME} -- etcdctl --ca-file /etc/kubernetes/pki/etcd/ca.crt --cert-file /etc/kubernetes/pki/etcd/peer.crt --key-file /etc/kubernetes/pki/etcd/peer.key --endpoints=https://${CP0_IP}:2379 member add ${CP2_HOSTNAME} https://${CP2_IP}:2380
kubeadm alpha phase etcd local --config kubeadm-master.config
```

add node :
```
kubeadm alpha phase kubeconfig all --config kubeadm-master.config
kubeadm alpha phase controlplane all --config kubeadm-master.config
kubeadm alpha phase mark-master --config kubeadm-master.config
```

network plugin :
```
mkdir flannel && cd flannel
wget https://raw.githubusercontent.com/coreos/flannel/v0.10.0/Documentation/kube-flannel.yml
```

vi kube-flannel.yml
```
containers:
  - name: kube-flannel
    command:
    - /opt/bin/flanneld
    args:
    - --ip-masq
    - --kube-subnet-mgr
    - --iface=ens160
```

```
kubectl apply -f kube-flannel.yml
```

after cluster built (run on all nodes):
```
mkdir ~/.kube/
cp /etc/kubernetes/admin.conf ~/.kube/config
```

allow master to be a worker :
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

problems may face :
Node show no ready when VIP of keepalived apply on it
check /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
add --node-ip=192.168.x.x to the end, example :
```
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_NETWORK_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CERTIFICATE_ARGS $KUBELET_EXTRA_ARGS --cloud-provider=vsphere --cloud-config=/etc/vsphereconf/vsphere.conf --node-ip=192.168.0.2
```
Failed to list *v1.Node: Get https://10.96.0.1:443/api/v1/nodes?resourceVersion=0: net/http: TLS handshake timeout
run
```
iptables -P FORWARD ACCEPT on all node to solve
```


reference link :
[https://www.kubernetes.org.cn/4256.html](https://www.kubernetes.org.cn/4256.html)
