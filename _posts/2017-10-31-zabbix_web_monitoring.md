---
layout: posts
title: zabbix web monitoring with alert trigger
image: ../img/zabbix.png
tags: zabbix monitoring config
category: zabbix
---

#### 1. add host

configuration > hosts > create host > host name (for example : test.local)

new groups : webserver

agent interfaces
ip address : 127.0.0.1  &lt;-- just add ip, doesn't need agent

update

#### 2. add web scenarios

configuration > hosts > click web at your created host > create web scenario

scenario
name : availability of test.local
new application : web_checks
Agent : select browser you wanted to check with

Steps
add
name : index
URL : <https://test.local/>
required status codes : 200
update

#### 3. add trigger

configuration > hosts > triggers >  create trigger
name :  
Web scenario "Availability of test.local" failed: {ITEM.VALUE}

severity :
high

expression :
{test.local:web.test.error[Availability of test.local].strlen()}>0 and {test.local:web.test.fail[Availability of test.local].min(1)}>0

update

> replace test.local to your host name above
