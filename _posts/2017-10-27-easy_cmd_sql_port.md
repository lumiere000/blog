---
layout: article
title: allow sql port by windows command prompt
cover: /img/sql.webp
tags: windows sql firewall security
category: sql
---

run cmd as admin

```markdown
netsh firewall set portopening protocol = TCP port = 1433 name = SQLPort mode = ENABLE scope = SUBNET profile = CURRENT
netsh advfirewall firewall add rule name = SQLPort dir = in protocol = tcp action = allow localport = 1433 remoteip = localsubnet profile = DOMAIN
```
