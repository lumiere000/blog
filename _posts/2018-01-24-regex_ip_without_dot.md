---
layout: article
title: Regular expression replacement for ip without dot
cover: /img/tools.jpg
tags: website tools scripts
category: scripts
---

String:

```
192.168.0.2
```

regular expression :

```
(\d+).(\d+).(\d+).(\d+)
```

substitution :

```
\1\2\3\4
```

result :

```
19216802
```

every ( ) represent a group, \d+ means any digits, all the line mean there are four groups between "." and those group can only be digits.

subsitution simply show goup1 group2 group3 group4
