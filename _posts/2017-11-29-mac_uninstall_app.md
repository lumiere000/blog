---
layout: article
title: fully uninstall a program in mac os x by using terminal
cover: /img/apple.webp
tags: mac terminal uninstall
category: apple
---

After uninstall a program like anti-virus or smartsheild, you still see it auto start after reboot, they usually hide in /system/library/cache or /system/library/user. To find them, use your terminal type this command :

```
find / -name "your_target_program"
```

then remove by :

```
sudo rm -rif /path/to/your/target
```
