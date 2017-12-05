get your vsphere login with read only permission for the macros.
Create a esxi host with Template Virt VMware, set following :

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

also see zabbix_server.conf for docker : [../_posts\2017-12-05-zabbix_vm_monitoring.md] [-zabbix_vm_monitoring]
