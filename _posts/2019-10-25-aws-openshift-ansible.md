---
layout: article
title: AWS Openshift cluster build by ansible
#image: /img/aws.png
tags: config openshift aws cloud
category: cloud
cover: /img/aws.png
---

### this is a Openshift cluster build demo by ansible uses US-West AWS site

### EC2 example of US-West(N. California)

|role|type|cpu|ram|
|---|---|---|---|
|master01|r5a.xlarge|4|32|
|infra01|t3a.xlarge|4|16|
|compute01|c5.4xlarge|16|32|
|compute02|c5.4xlarge|16|32|
|compute03|c5.4xlarge|16|32|

### enable aws password ssh

```
passwd centos
```

vi /etc/ssh/sshd_config

```
PasswordAuthentication yes
service sshd restart
```

Type|Protocol|Port Range|Source|Description
---|---|---|---|---
HTTP|TCP|80|0.0.0.0/0|public to web
All traffic|All|All|172.31.0.0/16|openshift internal
SSH|TCP|22|X.X.X.X/32|where you ssh from
Custom TCP Rule|TCP|8443|0.0.0.0/0|webconsole needs open for oauth
HTTPS|TCP|443|0.0.0.0/0|public to web

### add Elastic IPs

```
Allocate new address, one for infra, one for master
```

### change hostname and hosts
vi /etc/hosts

```
172.31.0.1 master01.openshift.local
172.31.0.2 infra01.openshift.local
172.31.0.11 compute01.openshift.local
172.31.0.12 compute02.openshift.local
172.31.0.13 compute03.openshift.local
```

### install ansible

```
sudo yum install epel-release
sudo yum install ansible
```

### copy ssh key to master01

```
scp -i openshift.pem ./openshift.pem centos@master01.openshift.local:/tmp
mv /tmp/id_ras /root/.ssh/
```

### config ssh method

```
cat <<EOF > /root/.ssh/config
Host master01.openshift.local
  Hostname 172.31.0.1
  User centos
  IdentityFile /root/.ssh/openshift.pem

Host infra01.openshift.local
  Hostname 172.31.0.2
  User centos
  IdentityFile /root/.ssh/openshift.pem

Host compute01.openshift.local
  Hostname 172.31.0.11
  User centos
  IdentityFile /root/.ssh/openshift.pem

Host compute02.openshift.local
  Hostname 172.31.0.12
  User centos
  IdentityFile /root/.ssh/openshift.pem

Host compute03.openshift.local
  Hostname 172.31.0.13
  User centos
  IdentityFile /root/.ssh/openshift.pem
EOF
```

### set hostname (on every node) for example:

```
hostnamectl set-hostname master01.openshift.local
```

### set timezone

```
timedatectl set-timezone Asia/Hong_Kong
```

### install openshift require utils

```
yum install -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct NetworkManager firewalld python-passlib httpd-tools
systemctl enable NetworkManager && systemctl start NetworkManager
systemctl enable firewalld && systemctl start firewalld
yum -y update && systemctl daemon-reload
```

### copy openshift-ansible playbook

```
cd ~
git clone -b release-3.11 https://github.com/openshift/openshift-ansible && cd openshift-ansible
```

### config inventory

```
cat <<EOF > ~/openshift-ansible/inventory.cfg
[OSEv3:children]
masters
nodes
etcd

[OSEv3:vars]
ansible_ssh_user=centos
ansible_sudo=true
ansible_become=true
openshift_docker_insecure_registries=172.30.0.0/16
openshift_deployment_type=origin
os_firewall_use_firewalld=true
openshift_master_cluster_public_hostname=webconsole.yourdomain.com
openshift_master_default_subdomain=yourdomain.com
openshift_clock_enabled=true
openshift_disable_check=docker_storage
#openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider’, 'filename': '/etc/origin/master/htpasswd}]

# Install Metrics
openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}
openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
openshift_metrics_install_metrics=True
openshift_metrics_hawkular_hostname=hawkular-metrics.yourdomain.com
openshift_master_metrics_public_url=https://hawkular-metrics.yourdomain.com/hawkular/metrics
openshift_metrics_cassandra_pvc_size=10G
openshift_metrics_hawkular_replicas=2
openshift_metrics_image_version=v3.11

# host group for masters
[masters]
master01.openshift.local

# host group for etcd
[etcd]
master01.openshift.local

[nodes]
master01.openshift.local openshift_node_group_name='node-config-master'
infra01.openshift.local openshift_node_group_name='node-config-infra'
compute01.openshift.local openshift_node_group_name='node-config-compute'
compute02.openshift.local openshift_node_group_name='node-config-compute'
compute03.openshift.local openshift_node_group_name='node-config-compute'
EOF
```

### master Install Java JDK for Ansible playbook

```
yum install java-1.8.0-openjdk-headless
```

### create htpasswd account for webconsole login
### (-c option is only for initial, please DONOT use again after first user create, or all user password will lost)

```
htpasswd -c /etc/origin/master/htpasswd admin
chmod 640 /etc/origin/master/htpasswd
```

### start preinstall checking and deploy

```
ansible-playbook -i inventory.cfg playbooks/prerequisites.yml
ansible-playbook -i inventory.cfg playbooks/deploy_cluster.yml
```

### import Cert using AWS Certificate Manager
require cert body, key and chain

### create Load Balancer and Target Groups
create Load Balancer and point it to infra node, for holding SSL cert
Target Groups also point to infra node

### fix abs image pull error (if abs pod crash after cluster built)

```diff
vi roles/ansible_service_broker/defaults/main.yml
- origin: 'docker.io/ansibleplaybookbundle/origin-ansible-service-broker:${version}'
+ origin: 'docker.io/ansibleplaybookbundle/origin-ansible-service-broker:latest'
```

### login by oc command

```
oc login admin
oc adm policy add-cluster-role-to-user cluster-monitoring-view admin
```

### login superuser

```
oc login -u system:admin
```

### add user to admin group

```
oc adm policy add-cluster-role-to-user admin admin
```

### list all monitor system

```
oc -n  openshift-monitoring get routes
```

### add compute procedure

### add new node info to

```
/root/.ssh/config
```

### install ansible require utils

```
yum install -y yum-utils device-mapper-persistent-data lvm2 chrony nano wget
yum install -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct NetworkManager firewalld python-passlib
yum install python-docker-py
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
systemctl enable NetworkManager && systemctl start NetworkManager
systemctl enable firewalld && systemctl start firewalld
yum -y update && systemctl daemon-reload
```

### add new node info to inventory.cfg file

```
vi inventory.cfg
[new_nodes]
new_node.domain
/etc/hosts on all nodes
<new_node_ip> new_node.domain
```

### run deploy after the prerequistites has no problem

```
ansible-playbook -i inventory.cfg playbooks/prerequisites.yml
ansible-playbook -i inventory.cfg playbooks/deploy_cluster.yml
```

### noschedule a node

```
oc adm taint nodes node1 key=value:NoSchedule
```

### remove the noschedule

```
oc adm taint nodes node1 key:NoSchedule-
```

### delete node (Warning: this cannot be undone)

```
oc delete node <node>
```

### uninstall cluster (Warning: this will destory everything)

```
ansible-playbook -i inventory.cfg playbooks/adhoc/uninstall.yml
```
