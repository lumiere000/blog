---
layout: article
title: what is my ip address for linux
cover: /img/tools.webp
tags: tools scripts ubuntu shellscript google
category: tools
---

There is a way to find your outgoing/public ip using google's ns server, for security reason I do not recommend using curl/wget, using dig command is better :

```
dig TXT +short o-o.myaddr.l.google.com @ns1.google.com | awk -F'"' '{ print $2}'
```

reference link :

[https://www.cyberciti.biz/faq/how-to-find-my-public-ip-address-from-command-line-on-a-linux/](https://www.cyberciti.biz/faq/how-to-find-my-public-ip-address-from-command-line-on-a-linux/)
