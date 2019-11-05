---
layout: article
title: linux boot partition full
cover: /img/ubuntu.webp
tags: ubuntu boot kernel partition system
category: ubuntu
---

Ubuntu cannot update because of boot partition full, take a snapshot before system change is always good.

get kernel images not running :
```
dpkg --list 'linux-image*'|awk '{ if ($1=="ii") print $2}'|grep -v `uname -r`
```

output like below :

```
 linux-image-4.4.0-128-generic
 linux-image-extra-4.4.0-124-generic
 linux-image-extra-4.4.0-127-generic
 linux-image-extra-4.4.0-128-generic
 linux-image-4.4.0-130-generic
 linux-image-extra-4.4.0-130-generic
 initramfs-tools
```

delete package :

```
rm -rf /boot/*-4.4.0-{103,104,108,109,112,116,121,124}-*
```

fix kernel dependencies :

```
apt-get -f install
```

now you can update and dis-upgrade again
