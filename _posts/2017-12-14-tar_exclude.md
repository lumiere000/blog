---
layout: article
title: tar with multi directory exclude
cover: /img/tools.webp
tags: tools scripts ubuntu shellscript
category: tools
---

There may be some tmp or cache folder you don't need while backup, here is a simple script for tar and exclusion.

Place it to same path to the folder you want to tar, fill in the variable below, src is target folder, dest is output path, exc1 is your first excluded folder, exc2 is second, you can extend exc3 by adding variable and add --exclude=$exc3 to main script if you want.

vi tar_backup.sh
copy and paste below
```
#!/bin/sh
src="./sourcefolder"
dest="./destinationfolder/folder.tar.gz"
exc1="./sourcefolder/foldertoexclude1"
exc2="./sourcefolder/folderto/exclude2"

tar -cvzf $dest --exclude=$exc1 --exclude=$exc2 $src
```

```chmod 755 ./tar_backup.sh```

run it by

```./tar_backup.sh```
