---
layout: article
title: docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu
cover: /img/docker.png
tags: docker zabbix postgresql ubuntu monitoring
category: docker
---

#### 1. download the dockerfiles and structure :

[dockerfiles&structure ](https://github.com/lumiere000/blog/blob/master/dockerize/readme.md)

structure :

```markdown
/home/zabbix
        ├── agent
        ├── server-pgsql
        ├── snmptraps
        ├── web-nginx-pgsql
        └── zbx_env
              ├── etc
              ├── usr
              └── var
```

#### 2. place docker-compose.yml

place [docker-compose.yml](https://github.com/lumiere000/blog/blob/master/dockerize/docker-compose.yml) to /home/zabbix

or
simply
```
cd /home
wget https://github.com/lumiere000/blog/raw/master/dockerize/zabbix.tar.gz
tar -zxv -f zabbix.tar.gz -C .
```

#### 3. build and start

add your timezone before compose up if you are using centos
```
echo "your_timezone" > /etc/timezone
```

```markdown
cd /home/zabbix
docker-compose up -d
```

### useful things

* * *

#### PHP time zone config

goto

```markdown
/home/zabbix/web-nginx-pgsql/ubuntu/conf/etc/php5/fpm/conf.d
vi 99-zabbix.ini
```

modify

```markdown
date.timezone=Asia/Hong_Kong
```

* * *

#### rebuild container without cache

only use it if you want to rebuild everything, it takes a long time

```markdown
docker-compose build --no-cache
```
