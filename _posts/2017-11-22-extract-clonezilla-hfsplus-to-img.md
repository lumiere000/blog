---
layout: article
title: combine and extract clonezilla gzip file to img file
cover: /img/ubuntu.png
tags: ubuntu installation tools clonezilla backup restore
category: ubuntu
---

install partclone by :
```
apt-get update
apt-get install partclone
```
combine and extract clonezilla gzip file to img file :
```
cat sda1.hfsplus-ptcl-img.gz.a* | gzip -d -c | partclone.restore -W -o /tmp/sda1.img -L /tmp/partclone.log
```

image extracted can browse with hsexplorer:

for windows browsing mac os x hfsplus image

reference link :
[http://www.catacombae.org/hfsexplorer/](http://www.catacombae.org/hfsexplorer/)
