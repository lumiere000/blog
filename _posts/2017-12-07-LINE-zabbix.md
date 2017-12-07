---
layout: posts
title: use line messager for zabbix alert
image: /img/line.png
tags: zabbix line ubuntu monitoring alert
---

login and get a line token from https://notify-bot.line.me
click your name > My page > Generate token > select a group or 1-on-1 means send to you only


vi /usr/lib/zabbix/alertscripts/LINE_Notify.sh

```
#!/bin/sh
export PATH="/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin"
export LANG=C

# LINE Notify API access token
access_token="paste your token here"

# message title
subject=$2

# content
body=$3

# run LINE Notify
curl -k -X POST -H "Authorization: Bearer ${access_token}" -F "message=${subject}: ${body}" https://notify-api.line.me/api/notify
```

chmod 755 /usr/lib/zabbix/alertscripts/LINE_Notify.sh

go to zabbix administration > media types > create media types

```
Name : Line Notify
Type : Script
Script name : line_notify.sh
Script parameters :
{ALERT.SENDTO}
{ALERT.SUBJECT}
{ALERT.MESSAGE}
```

go to zabbix configuration actions > create action

```
Name : Line Alert to Zabbix administrators
Conditions :
A	Host group = the host group you want to monitoring

Operations details :
Send to User groups : Zabbix administrators
Send only to Line notify
```

go to zabbix Administration > Users > Admin > Media > Add

Type : Line notify
Send to : Admin < this will auto change to your token when action triggered

you can test line notfiy api by running

```
/usr/lib/zabbix/alertscripts/LINE_Notify.sh
```

for troubleshooting you can goto zabbix Reports > Action log
to see your message status

You need curl for line notify api to run, install by

```
apt-get install curl
```

or modify your Dockerfile

reference link :

https://blog.apar.jp/zabbix/5892/

http://chienleebug.blogspot.hk/2017/03/zabbixalarm-to-line-notify.html
