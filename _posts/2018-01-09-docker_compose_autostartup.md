---
layout: article
title: docker compose auto start after reboot host
image: /img/docker.png
tags: docker installation ubuntu crontab config
category: docker
---

add docker.service to system startup first :

```
systemctl enable docker.service
```

detail see : [systemctl usage](../2018-01-09-systemctl.html)


add restart: always like this :

```
mysql:
  env_file: .env_mysql
  image: mysql:5.6
  restart: always
  volumes:
    - /home/database:/var/lib/mysql
  env_file:
    - mysql_env
```

add cron job, replace directory_has_dockercomposeyml to your path

```
crontab -e

@reboot (sleep 30s ; cd directory_has_dockercomposeyml ; /usr/local/bin/docker-compose up -d )&
```

PS. custom network driver bridge does need "restart: always"

reference link :

[https://stackoverflow.com/questions/43671482/how-to-run-docker-compose-up-d-at-system-start-up](https://stackoverflow.com/questions/43671482/how-to-run-docker-compose-up-d-at-system-start-up)

[https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)
