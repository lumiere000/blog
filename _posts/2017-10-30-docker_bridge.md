---
layout: posts
title: docker bridge
image: ../img/docker.png
---

useful when there is no nmlic and brctl installed or configured but still bridge running, and they still here after reboot

show docker controlled network

```markdown
docker network ls
```

delete non-using docker bridge

```markdown
docker network prune
```
