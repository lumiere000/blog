---
layout: article
title: fallocate
cover: /img/ubuntu.png
tags: security tools filesystem
category: tools
---

Not enough space in system disk, will cause cannot boot and many problems, there is a stupid but easy way to prevent, use fallocate to create a 1GB file, you can delete it to free the disk when there is urgent case.


fallocate command basic syntax :

```
fallocate -l Image_Size_Here /path/to/image.img
```

for example, create a 1GB file :

```
fallocate -l 1G test.img
```

reference link :

[https://www.cyberciti.biz/faq/howto-create-lage-files-with-dd-command/](https://www.cyberciti.biz/faq/howto-create-lage-files-with-dd-command/)
