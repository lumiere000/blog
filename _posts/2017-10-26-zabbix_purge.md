---
layout: article
title: zabbix purge history with postgresql
image: /img/zabbix.png
tags: zabbix postgresql shellscript tools
category: zabbix
---

🔺 🔺 🔺 **_please make sure you want to delete history of zabbix before you execute files in this procedure_** 🔺 🔺 🔺

#### create and config sh file

vi purge.sh

```
#!/bin/bash

read -r -p "Are You Sure to purge old data from zabbix? [Y/n] " input

case $input in
    [yY][eE][sS]|[yY])
 echo "purge start"
psql -Uzabbix -dzabbix -a -f "./purge.sql"
 echo "purge finished"
 ;;

    [nN][oO]|[nN])
 echo "operation cancelled"
       ;;

    *)
 echo "Invalid input...operation cancelled"
 exit 1
 ;;
esac
```

#### create postgresql procedure file

vi purge.sql

```
delete FROM alerts where age(to_timestamp(alerts.clock)) > interval '3 days';

delete FROM acknowledges where age(to_timestamp(acknowledges.clock)) > interval '3 days';

delete FROM events where age(to_timestamp(events.clock)) > interval '3 days';

delete FROM history where age(to_timestamp(history.clock)) > interval '3 days';
delete FROM history_uint where age(to_timestamp(history_uint.clock)) > interval '3 days' ;
delete FROM history_str  where age(to_timestamp(history_str.clock)) > interval '3 days' ;
delete FROM history_text where age(to_timestamp(history_text.clock)) > interval '3 days' ;
delete FROM history_log where age(to_timestamp(history_log.clock)) > interval '3 days' ;

delete FROM trends where age(to_timestamp(trends.clock)) > interval '30 days';
delete FROM trends_uint where age(to_timestamp(trends_uint.clock)) > interval '30 days' ;


delete from history where itemid not in (select itemid from items where status='0');
delete from history_uint where itemid not in (select itemid from items where status='0');
delete from history_str where itemid not in (select itemid from items where status='0');
delete from history_text where itemid not in (select itemid from items where status='0');
delete from history_log where itemid not in (select itemid from items where status='0');

delete from trends where itemid not in (select itemid from items where status='0');
delete from trends_uint where itemid not in (select itemid from items where status='0');
```

#### 3 days history and 30 days trends will keep in this case
