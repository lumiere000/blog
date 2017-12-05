To use VMware simple checks the host must have the following user macros defined,
after import these 3 templates, go to configuration > templates
select Template Virt VMware > Macros

{$URL} - VMware service (vCenter or ESX hypervisor) SDK URL (https://servername/sdk).
{$USERNAME} - VMware service user name
{$PASSWORD} - VMware service {$USERNAME} user password

also add these 3 macros to Template Virt VMware Hypervisor and Template Virt VMware Guest

Template virt VMware using Low-level discovery, discovered guest will use Template Virt VMware Guest, discovered host will use Template Virt VMware Hypervisor.

This template only contain auto discover and performance monitor, trigger and alert need another config.

also see zabbix_server.conf for docker : [../_posts\2017-12-05-zabbix_vm_monitoring.md] [-zabbix_vm_monitoring]
