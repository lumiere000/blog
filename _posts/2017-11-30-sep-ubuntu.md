---
layout: posts
title: Symantec endpoint ubuntu installation
image: /img/symantec.png
tags: symantec endpoint installation ubuntu x64
---

install 32bit library before you start : (those are i386 libraries, cannot search by apt-cache search)

```
sudo apt-get install ia32-libs
sudo apt-get install libc6:i386 libx11-6:i386 libncurses5:i386 libstdc++6:i386
sudo apt-get install lib32ncurses5 lib32z1
```

upzip the install package,

```
chmod u+x install.sh
sudo ./install.sh -i
```


reference link :

[https://support.symantec.com/en_US/article.TECH228118.html](https://support.symantec.com/en_US/article.TECH228118.html)
