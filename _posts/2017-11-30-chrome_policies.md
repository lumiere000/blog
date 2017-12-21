---
layout: posts
title: chrome policies delete
image: /img/chrome.jpg
tags: security tools website windows batch
category: security
---

Some adware or homepage kidnap software may using chrome policies, after clearing the extensions and temp, cache, even reinstall chrome cannot solve the problem, you may try this.

create this batch file and run as admin. Or run cmd as admin then cd to this file location and execute.

```
@echo off

IF NOT EXIST %WINDIR%\System32\GroupPolicy goto next

echo Deleting GroupPolicy folder...
RD /S /Q "%WINDIR%\System32\GroupPolicy" || goto error
echo.

:next
IF NOT EXIST %WINDIR%\System32\GroupPolicyUsers goto next2

echo Deleting GroupPolicyUsers folder...
RD /S /Q "%WINDIR%\System32\GroupPolicyUsers" || goto error
echo.

:next2
gpupdate /force

pause
exit

:error
echo.
echo An unexpected error has occurred.  opened the program as an administrator (right click, run as administrator)?
echo.
pause
exit
```
