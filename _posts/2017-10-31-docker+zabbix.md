---
layout: posts
title: docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu
image: img/default.png
---

## docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu
---
#### 1. download the dockerfiles and structure :
[dockerfiles&structure ](dockerize/readme.md)

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

place [docker-compose.yml](dockerize/docker-compose.yml) to /home/zabbix


#### 3. build and start
```markdown
cd /home/zabbix
docker-compose start -d
```


### useful things
---
##### PHP time zone config
goto
```markdown
/home/zabbix/web-nginx-pgsql/ubuntu/conf/etc/php5/fpm/conf.d
vi 99-zabbix.ini
```
modify
```markdown
date.timezone=Asia/Hong_Kong
```

##### rebuild container without cache
```markdown
docker-compose build --no-cache
```

