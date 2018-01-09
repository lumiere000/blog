---
layout: posts
title: systemctl usage
image: /img/ubuntu.png
tags: shellscript ubuntu
category: ubuntu
---

chkconfig no longer available in Ubuntu, systemctl is an alternate option for Ubuntu. Unlike sysv-rc-conf, it is an offical way.

List service name :

```
systemctl list-units
```

can use grep like :

```
systemctl list-units |grep "docker"
```

or use / after systemctl list-units

use enable to tell systemd to start services automatically at boot, like "chkconfig on"

```
systemctl enable docker.service
```

disable auto start at boot :

```
systemctl disable docker.service
```

Checking the Status of Services :

```
systemctl status docker.service
```

reference link :

[https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)
