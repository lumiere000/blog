---
layout: posts
title: elasticsearch+logstash+kibana
image: /img/elasticsearch.png
---

1. download the zip file from : [https://github.com/deviantony/docker-elk]()
2. unzip to /home/elk/

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
3. modify elasticsearch volumes :
      volumes:
      - ./elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - ./elasticsearch/data:/usr/share/elasticsearch/data
4. build and run
```
cd /homoe/elk
docker-compose up -d
```
5. config and data files:
  ```
   elasticsearch config file:
  /home/elk/elasticsearch/config/elasticsearch.yml
   kibana config file:
  /home/elk/kibana/config/kibana.yml
   elasticsearch data folder:
  /home/elk/elasticsearch/data
  ```
