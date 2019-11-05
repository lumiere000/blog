---
layout: article
title: use vmcli poweroff guest in vcenter/hypervisor
cover: /img/vm.webp
tags: vmware powershell tools installation windows
category: vmware
---

A powershell script can auto detect vm guest which power on, then shutdown and wait until they are all complete. After vm guest shutdown confirmed, hypervisor will shutdown with confirmation.  

create a powershell script file and name it to shutdown.ps1

```
$vsphere="vcentername/ip"
$hypervisor="hypervisorname/ip"

$cred = Get-Credential

Get-Module –ListAvailable VMware.VimAutomation.Core* | Import-Module

connect-viserver -server "$vsphere" -Credential $cred

$vmservers=get-vm -location "$hypervisor" | Where-Object {$_.powerstate -eq ‘PoweredOn’}

$vmservers | select Name | export-csv c:\temp\servers.csv -NoTypeInformation

Write-Host "VMGuest are going to shutdown"

(Read-Host 'Press Enter to continue…')

$vmservers | Shutdown-VMGuest -Confirm:$false

#Waiting for Shutdown to complete

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

Write-Host "Guest Shutdown Success"

Write-Host "VMHost Shutdown Processing"

Stop-VMHost $hypervisor -Confirm

Write-Host "VMHost Shutdown Success"

(Read-Host 'Press Enter to exit…')

exit

```
