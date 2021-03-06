---
layout: article
title: elasticsearch+logstash+kibana
cover: /img/elasticsearch.webp
tags: docker elasticsearch logstash kibana
category: docker
---

download the zip file from : [https://github.com/deviantony/docker-elk](https://github.com/deviantony/docker-elk)

unzip to /home/elk/

```
Folder Structure :
/home/elk
       ├── elasticsearch
       │   ├── config
       │   └── data
       ├── extensions
       │   └── logspout
       ├── kibana
       │   └── config
       └── logstash
           ├── config
           └── pipeline
and don't forget .env and .travis.yml
```

modify elasticsearch volumes :

```
volumes:
 - ./elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
 - ./elasticsearch/data:/usr/share/elasticsearch/data
```

build and run

```
cd /home/elk
docker-compose up -d
```

config and data files:

```
elasticsearch config file:
/home/elk/elasticsearch/config/elasticsearch.yml
kibana config file:
/home/elk/kibana/config/kibana.yml
elasticsearch data folder:
/home/elk/elasticsearch/data
```
