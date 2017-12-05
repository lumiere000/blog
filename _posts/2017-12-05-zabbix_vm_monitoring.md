---
layout: posts
title: docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu
image: /img/zabbix.png
tags: docker zabbix ubuntu monitoring vmware
---
#### vm monitoring

according to our docker+zabbix post, there is an advanced use to monitor vmware
[docker+zabbix+postgresql+snmptrap+zabbix_agent for ubuntu](../2017-10-31-docker+zabbix.md)

#### docker or zabbix_server.conf setting

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

#### zabbix template

there is an enhanced template of vmware monitoring and auto discover
get it here : [https://github.com/lumiere000/blog/tree/master/zabbix_template/Template-Virt-VMware][https://github.com/lumiere000/blog/tree/master/zabbix_template/Template-Virt-VMware]

get your vsphere login with read only permission and fill in the macros.
