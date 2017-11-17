---
layout: posts
title: check mounted vmdk filesystem on ubuntu
image: ../img/default.png
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
