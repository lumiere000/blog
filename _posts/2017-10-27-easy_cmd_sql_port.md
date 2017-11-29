---
layout: posts
title: allow sql port by windows command prompt
image: /img/sql.png
tags: windows sql firewall security
---

run cmd as admin

```markdown
netsh firewall set portopening protocol = TCP port = 1433 name = SQLPort mode = ENABLE scope = SUBNET profile = CURRENT
netsh advfirewall firewall add rule name = SQLPort dir = in protocol = tcp action = allow localport = 1433 remoteip = localsubnet profile = DOMAIN
```
