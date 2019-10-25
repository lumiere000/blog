---
layout: article
title: docker exec error
cover: /img/docker.png
tags: docker installation ubuntu
category: docker
---

error when you exec some docker container :

rpc error: code = 2 desc = oci runtime error: exec failed: container_linux.go:247: starting container process caused "exec: \"bash\": executable file not found in $PATH"

this error may appear when your container doesn't have /bin/bash

solution :

use

```
docker exec -it container_id /bin/sh
```

instead of

```
docker exec -it container_id bash
```
