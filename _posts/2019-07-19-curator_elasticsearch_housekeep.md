---
layout: posts
title: curator elasticsearch housekeep
image: /img/elasticsearch.png
tags: installation config tools
category: tools
---

```
apt install python-pip
pip install elasticsearch-curator

vi /srv/curator/curator.yml
```

```
---
client:
  hosts:
    - 127.0.0.1
  port: 9200
  url_prefix:
  use_ssl: False
  certificate:
  client_cert:
  client_key:
  ssl_no_validate: False
  http_auth:
  timeout: 30
  master_only: False

logging:
  loglevel: INFO
  logfile:
  logformat: default
  blacklist: ['elasticsearch', 'urllib3']

```

vi /srv/curator/delete_index.yml

```
---
actions:
  1:
    action: delete_indices
    description: >-
      Delete indices older than 14 days (based on index name), for logstash-
      prefixed indices. Ignore the error if the filter does not result in an
      actionable list of indices (ignore_empty_list) and exit cleanly.
    options:
      ignore_empty_list: True
      timeout_override:
      continue_if_exception: False
      disable_action: False
    filters:
    - filtertype: pattern
      kind: regex
      value: '^[a-zA-Z0-9].*$'
      exclude: false
    - filtertype: age
      source: name
      direction: older
      timestring: '%Y.%m.%d'
      unit: days
      unit_count: 14
      exclude:
```

crontab -e

```
00 8 * * * root curator /srv/curator/delete_index.yml --config /srv/curator/curator.yml
```

config :

```
delete_index.yml
unit_count: 14
means delete data older than 14 days
```

### things need to know
```
- filtertype: pattern
  kind: regex
  value: '^[a-zA-Z0-9].*$'
  exclude: false
```

value above means everything with letter and number begin, that means it will delete everything except special character/symbol begin with
