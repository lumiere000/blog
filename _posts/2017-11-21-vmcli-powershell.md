---
layout: posts
title: use vmcli to get vminfo
image: /img/vm.jpg
---

use vmcli to get both assigned and used cpu memory and harddisk information and gen a csv report

1. install Windows Management Framework 4.0 : [https://www.microsoft.com/en-us/download/details.aspx?id=40855](https://www.microsoft.com/en-us/download/details.aspx?id=40855) (don't try to get 3.0, the patch cause my windows 7 sp1 crashed)
2. install vmware powercli 6.5 : [https://my.vmware.com/web/vmware/details?downloadGroup=PCLI650R1&productId=614](https://my.vmware.com/web/vmware/details?downloadGroup=PCLI650R1&productId=614)
3. open vmware powercli (not using powershell)
4. create a powershell file, in this case I named it getvminfo.ps1, place it to c:

```
connect-viserver -server yourvmname_or_ip
Get-View -ViewType VirtualMachine | %{
New-Object PSObject -Property @{
Name = $_.Name
Host = (Get-View $_.Summary.Runtime.Host).Name
Datastore = [system.String]::Join(",",($_.Storage.PerDatastoreUsage | %{Get-View $_.Datastore} | %{$_.Name}))
Size = (Get-VM $_.Name | Select-Object -Property ProvisionedSpaceGB).ProvisionedSpaceGB #($_.Storage.PerDatastoreUsage | Measure-Object -Property Committed -Sum).Sum
Usedspace = (Get-VM $_.Name | Select-Object -Property UsedSpaceGB).UsedSpaceGB
CPU = (Get-VM $_.Name | Select-Object -Property numCPU).NumCpu
Memory = (Get-VM $_.Name | Select-Object -Property MemoryMB).MemoryMB
Network = (Get-NetworkAdapter $_.Name | Select-Object -Property NetworkName).NetworkName
usedMemory = [Math]::Round(((Get-VM $_.Name | Get-Stat -Stat mem.usage.average -Start (Get-Date).AddDays(-30) -IntervalMins 5 | Measure-Object Value -Average).Average),2)
}
} | Export-Csv "C:\VM-report.csv" -NoTypeInformation -UseCulture
```

reference link :
[http://vstrong.info/2014/11/18/powercli-average-cpu-memory-network-and-disk-usage/](http://vstrong.info/2014/11/18/powercli-average-cpu-memory-network-and-disk-usage/)
