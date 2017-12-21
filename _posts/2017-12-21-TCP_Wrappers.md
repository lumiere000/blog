---
layout: posts
title: TCP Wrappers
image: /img/ubuntu.png
tags: security monitoring network tools
category: security
---

using hosts.allow and hosts.deny to control sshd connection in ubuntu

please make sure your hosts.allow works first, or you will lost control after create hosts.deny.

adding your ip range which allow using sshd for example 192.168.0.1/24 :

```
vi /etc/hosts.allow
sshd: 192.168.0.1/24
```

deny all other ip using sshd :

```
vi /etc/hosts.deny
sshd:all
```

Other useful command :

find program full path by ps -auxwe
```
ps -auxwe |grep "target pid or port"
```

find all connection to your machine currently
```
netstat -tunp
```

reference link :

[https://www.centos.org/docs/rhel-rg-en-3/s1-tcpwrappers-access.html](https://www.centos.org/docs/rhel-rg-en-3/s1-tcpwrappers-access.html)
