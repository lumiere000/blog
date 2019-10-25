---
layout: article
title: python debug mode, also can print logs to log file
cover: /img/python.png
tags: python debug logs
category: python
---

place this on top

```markdown
import logging
logging.basicConfig(filename='/var/log/custom.log', level=logging.DEBUG,
format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
logging.debug('this is a debug message.')
```

place your debug message anywhere you like :

```markdown
logger.debug('this is the array: %s',sys.argv[1])
```

> in this case, "%s" will show array from "sys.argv[1]"
