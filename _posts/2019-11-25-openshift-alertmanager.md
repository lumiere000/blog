---
layout: article
title: Openshift alertmanager
tags: config openshift aws cloud
category: cloud
cover: /img/openshift.webp
---

### go to api.slack.com/apps to create new app, get a api url
[https://api.slack.com/apps](https://api.slack.com/apps)

### get existing secret (config) of alertmanager
```
oc -n openshift-monitoring get secret alertmanager-main --template='{{ index .data "alertmanager.yaml" }}' |base64 -d > alertmanager.yaml
```

### edit secret like this :
in this example, receiver slack will be default receivier, every alert didn't match, will receive by it.
DeadManSwitch is an alert tells the alert pipeline is still work.

```
global:
  slack_api_url: "https://hooks.slack.com/services/*******/*******/**************"

route:
  receiver: slack
  group_by: [Alertname]
  group_wait:      30s
  group_interval:  3m
  repeat_interval: 1h
  routes:
  - match:
      alertname: DeadMansSwitch
    group_wait: 0s
    group_interval: 1m
    repeat_interval: 24h
    receiver: dms

receivers:
- name: slack
  slack_configs:
  - channel: "server-alert"
    title: "Openshift {{ range .Alerts }}{{ .Annotations.summary }}\n{{ end }}"
    text: "{{ range .Alerts }}{{ .Annotations.description }}\n{{ end }}"
- name: dms
  slack_configs:
  - channel: "server-alert"
    api_url: "https://hooks.slack.com/services/********/********/*********************"
    title: "Openshift {{ range .Alerts }}{{ .Annotations.summary }}\n{{ end }}"
    text: "{{ range .Alerts }}{{ .Annotations.description }}\n{{ end }}"
```

### replace secret, normally will receive message after replace secret, it doesn't use test below
```
oc -n openshift-monitoring create secret generic alertmanager-main --from-file=alertmanager.yaml --dry-run -o=yaml |  oc -n openshift-monitoring replace secret --filename=-
```

### test
```
oc -n openshift-monitoring exec -it alertmanager-main-0 bash
curl -H "Content-Type: application/json" -d '[{"labels":{"alertname":"TestAlert1"}}]' localhost:9093/api/v1/alerts
```
