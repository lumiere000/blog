---
layout: article
title: service host local system and compatTelRunner
cover: /img/windows.png
tags: windows performance config
category: windows
---

Both "service host local system" and "compatTelRunner" are used for windows update.
They use up lots of resources for only checking the compatibility of windows update, especially from 7 to 10

Disable them by following steps:

1.  service host local system high usage

```markdown
disable superfatch in services.msc
```

2.  compatTelRunner

```markdown
taskschd.msc > Task Scheduler Library > Microsoft > Windows > Application Experience > disable "Microsoft Compatibility Appraice"
```
