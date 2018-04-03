---
layout: posts
title: remove non-using docker containers
image: /img/docker.png
tags: docker installation ubuntu script
category: docker
---

remove non-using docker containers with bash.

```
vi remove_container.sh
```

paste below :

```
#!/bin/bash

read -p "Press any key to remove non-using container, or ctrl+c to exit... " -n1 -s

echo "begin remove"

# Delete all stopped containers
docker ps -q -f status=exited | xargs --no-run-if-empty docker rm
# Delete all dangling (unused) images
docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi

echo "remove success"
```

add permission and run :

```
chmod 755 remove_container
./remove_container.sh
```

stop all containers

```
docker stop $(docker ps -a -q)
```

remove all containers

```
docker rm $(docker ps -a -q)
```
