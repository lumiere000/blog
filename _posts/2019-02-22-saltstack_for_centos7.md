---
layout: posts
title: installation and usage of saltstack in centos 7
image: /img/saltstack.png
tags: installation centos tools management cicd
category: cicd
---

saltstack - one of the alternatives of ansible, better function by installing agent (minion). 

TCP 4505、4506 port needed

### on all master and minion

```
yum install https://repo.saltstack.com/yum/redhat/salt-repo-latest-2.el7.noarch.rpm

yum install salt-minion

vi /etc/salt/minion
```

change
```
#master: salt
```
to
```
master: 192.168.1.101
```
(192.168.1.101 is master ip in this case)

```
systemctl start salt-minion.service
systemctl enable salt-minion.service
```

### on master

```
yum install salt-master

vi /etc/salt/master
```

change
```
#interface: 0.0.0.0
```
to
```
interface: 192.168.1.101
```

```
systemctl start salt-master.service
systemctl enable salt-master.service
```

### list and accept keys
```
salt-key -L
salt-key -A
```

### check
```
salt "*" test.ping
```

### usage
```
salt '*minion*' cmd.run 'df -h'
```

```
salt '*' cmd.run 'date'
```

reference link :

[https://docs.saltstack.com/en/latest/topics/installation/rhel.html](https://docs.saltstack.com/en/latest/topics/installation/rhel.html)
