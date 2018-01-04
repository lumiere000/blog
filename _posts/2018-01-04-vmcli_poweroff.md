---
layout: posts
title: use vmcli poweroff guest in vcenter/hypervisor
image: /img/vm.jpg
tags: vmware powershell tools installation windows
category: vmware
---

A powershell script can auto detect vm guest which power on, then shutdown and wait until they are all complete. 

create a powershell script file and name it to shutdown.ps1

```
$vsphere="vcentername/ip"
$hypervisor="hypervisorname/ip"

Get-Module –ListAvailable VMware.VimAutomation.Core* | Import-Module

connect-viserver -server $vsphere

$vmservers=get-vm -location “$hypervisor” | Where-Object {$_.powerstate -eq ‘PoweredOn’}

$vmservers | select Name | export-csv c:\servers.csv -NoTypeInformation

$vmservers | Shutdown-VMGuest

#Waiting for Shutdownn to complete

Write-host "Waiting for Shutdown to complete"

do

{

Write-Host "Waiting on shutdown for VM: "

#Check the power status
Write-Host "Shutting down:"
$status=get-vm -location “$hypervisor” | Where-Object {$_.powerstate -eq ‘PoweredOn’}
$status | select Name
sleep 5
}until([string]::IsNullOrWhiteSpace($status))

Write-Host "Shutdown Success"
```
