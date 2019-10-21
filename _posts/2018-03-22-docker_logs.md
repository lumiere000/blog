---
layout: article
title: docker logs and clear
image: /img/docker.png
tags: docker log
category: docker
---

you can check docker logs by following command, useful for troubleshooting like connection problem :

```
docker logs <container_id_or_name>
```

clear the log by :

```
{% raw %}echo "" > $(docker inspect --format='{{.LogPath}}' <container_id_or_name>){% endraw %}
```
