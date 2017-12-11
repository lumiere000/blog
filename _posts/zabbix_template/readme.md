useful templates for zabbix

please also import valuemap when use dell_idrac template

and add macros to dell_idrac template :

```
{$SNMP_COMMUNITY_IDRAC} => public
{$SNMP_COMMUNITY} => public
{$SNMP_PORT} => 161
```
