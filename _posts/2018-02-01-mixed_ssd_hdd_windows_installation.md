---
layout: article
title: mixed ssd and tradition hdd windows installation
image: /img/windows.png
tags: windows config installation ssd
category: windows
---

Dumb things like Samsung ultrabook 530u with a mixed hdd contain both tradition hdd and ssd hdd, you cannot install windows by a normal way.

1. Insert installation media (usb/dvd)
2. Select Custom install to view partitions.
3. Advanced > delete all partitions
4. Press Shift+F10 to launch cmd > diskpart
5. list disk, list part, usually tradition hdd will be disk 0 and ssd will be disk 1
6. select tradition hdd
```
sel disk 0
```
7. delete all partitions on it
```
clean
```
8. create 350MB partition
```
create part primary size=350
```
9. format
```
format fs=ntfs quick
```
10. assign letter
```
assign letter=f
```
11. Create partition fills rest of the disk
```
create part primary
```
12. format
```
format fs=ntfs quick
```
13. Assign letter c
```
assign letter=c
```
14. exit both diskpart and cmd, reboot to windows setup, delete the ssd partition that windows auto create and install windows to the large partition just create at step 11.

15. boot with installation media again, delete the ssd partition again and use Shift+F10
```
list vol
sel vol f
active
exit
bcdboot c:\windows /s f:
```

16. reboot and done
