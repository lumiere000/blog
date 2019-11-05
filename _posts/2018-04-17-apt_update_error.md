---
layout: article
title: apt-get update no public key
cover: /img/ubuntu.webp
tags: error install ubuntu shellscript ubuntu
category: ubuntu
---

The following signatures couldn't be verified because the public key is not available: NO_PUBKEY

just run :

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys yourpublickey
```

apt-get update

reference link :
[https://chrisjean.com/fix-apt-get-update-the-following-signatures-couldnt-be-verified-because-the-public-key-is-not-available/](https://chrisjean.com/fix-apt-get-update-the-following-signatures-couldnt-be-verified-because-the-public-key-is-not-available/)
