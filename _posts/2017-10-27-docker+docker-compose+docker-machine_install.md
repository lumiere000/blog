---
layout: posts
title: docker installation for ubuntu
image: img/default.png
---

## docker installation for ubuntu

```markdown
apt-get update

apt-get install apt-transport-https ca-certificates curl software-properties-common
    
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```markdown
apt-key fingerprint 0EBFCD88
```
##### verify key fingerprint:
Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88

```markdown
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

apt-get update

apt-get install docker-ce
```
install succeed if you see version by running :
```markdown
docker -v
```

## docker-compose installation for ubuntu
```markdown
curl -L https://github.com/docker/compose/releases/download/1.17.0-rc1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
install succeed if you see version by running :
```markdown
docker-compose -v
```

## docker-machine installation for ubuntu
```markdown
wget https://github.com/docker/machine/releases/download/v0.6.0/docker-machine-$(uname -s)-$(uname -m)

mv docker-machine-Linux-x86_64 docker-machine

chmod +x docker-machine

mv docker-machine /usr/local/bin/
```
install succeed if you see version by running :
```markdown
docker-machine -v
```
