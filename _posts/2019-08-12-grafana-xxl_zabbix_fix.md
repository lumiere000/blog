---
layout: posts
title: grafana-xxl zabbix plugin fix
image: /img/grafana.png
tags: config tools docker
category: monitoring
---

This is the fix of Zabbix plugin for Grafana error :

public/plugins/alexanderzobnin-zabbix-app/datasource-zabbix/module.js not found

after update

### edit docker-compose to disable plugin auto update (backup ./grafana/data first):

```
 grafana-xxl:
  image: monitoringartist/grafana-xxl:5.4
  ports:
   - "3000:3000"
  environment:
   - UPGRADEALL=false
  volumes:
   - ./grafana/data:/var/lib/grafana
  networks:
    zbx_net:
     aliases:
      - zabbix-server
      - zabbix-server-pgsql
      - zabbix-server-ubuntu-pgsql
      - zabbix-server-pgsql-ubuntu
      - postgres-server
      - pgsql-server
      - pgsql-database
```

### restart and recreate container

```
docker-compose up -d --force-recreate --build grafana-xxl
```

### reinstall plugin manually :

```
docker exec -it zabbix_grafana-xxl_1 bash

grafana-cli plugins list-remote
found zabbix app

grafana-cli plugins remove alexanderzobnin-zabbix-app

grafana-cli plugins install alexanderzobnin-zabbix-app 3.10.2
```

### you can try to restart docker-compose to verify plugin version now
