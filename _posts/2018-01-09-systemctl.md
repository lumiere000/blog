---
layout: article
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

result of status :
when docker is enabled :

```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2018-01-08 10:54:20 HKT; 1 day 4h ago
     Docs: https://docs.docker.com

```

when docker is disabled :

```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; disabled; vendor preset: enabled)
   Active: active (running) since Mon 2018-01-08 10:54:20 HKT; 1 day 4h ago
     Docs: https://docs.docker.com
```



reference link :

[https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)
