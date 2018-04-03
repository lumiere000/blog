---
layout: posts
title: docker exec error
image: /img/docker.png
tags: docker installation ubuntu
category: docker
---

error when you exec some docker container :

rpc error: code = 2 desc = oci runtime error: exec failed: container_linux.go:247: starting container process caused "exec: \"bash\": executable file not found in $PATH"

solution :

use
docker exec -it container_id /bin/sh

instead of
docker exec -it container_id bash
