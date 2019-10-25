---
layout: article
title: check mounted vmdk filesystem on ubuntu
cover: /img/ubuntu.png
tags: shellscript ubuntu
category: shellscript
---

```markdown
check inode used :
df -ih
will show something like :
/dev/mapper/ubuntu--vg-root
```

```markdown
show filesystem :
blkid /dev/mapper/ubuntu--vg-root
```
