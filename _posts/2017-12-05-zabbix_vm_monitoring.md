---
layout: posts
title: zabbix vm monitoring
image: /img/zabbix.png
tags: docker zabbix ubuntu monitoring vmware
---
### vm monitoring

according to our docker+zabbix post, there is an advanced use to monitor vmware
[docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu](../2017-10-31-docker+zabbix.html)

### docker or zabbix_server.conf setting

you can see 4 lines added to /home/zabbix/server-pgsql/ubuntu/run_zabbix_component.sh

```
# for vm
ZBX_STARTVMWARECOLLECTORS=${ZBX_STARTVMWARECOLLECTORS:-"250"}
ZBX_VMWAREFREQUENCY=${ZBX_VMWAREFREQUENCY:-"180"}
ZBX_VMWAREPERFFREQUENCY=${ZBX_VMWAREPERFFREQUENCY:-"180"}
ZBX_VMWARECACHESIZE=${ZBX_VMWARECACHESIZE:-"128M"}
```

equals to the config under /etc/zabbix/zabbix_server.conf :
```
StartVMwareCollectors=250
VMwareFrequency=180
VMwarePerfFrequency=180
VMwareCacheSize=128M
```

### zabbix template

there is an enhanced template of vmware monitoring and auto discover
get it here : [https://github.com/lumiere000/blog/tree/master/zabbix_template/Template-Virt-VMware]

get your vsphere login with read only permission for the macros.
Create a esxi host with Template Virt VMware, set following :

use {HOST.NAME} for your DNS name of agent and snmp interaces. It will let your discovered host use visible name for your agent and snmp interface.

To use VMware simple checks the host must have the following user macros defined,
after import these 3 templates, go to configuration > templates
select Template Virt VMware > Macros

{$URL} - VMware service (vCenter or ESX hypervisor) SDK URL (https://servername/sdk).

{$USERNAME} - VMware service user name

{$PASSWORD} - VMware service {$USERNAME} user password

also add these 3 macros to Template Virt VMware Hypervisor and Template Virt VMware Guest

Now wait about a minute, let your esxi host to login vsphere, it will get some basic information if it succeed. Then you need to wait a about an hour to auto discover and obtain information form vm guest.

Template virt VMware using Low-level discovery, discovered guest will use Template Virt VMware Guest, discovered host will use Template Virt VMware Hypervisor.

This template only contain auto discover and performance monitor, trigger and alert need another config.
