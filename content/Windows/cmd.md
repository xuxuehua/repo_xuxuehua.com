---
title: "cmd"
date: 2019-03-24 15:10
---


[TOC]



# CMD



## reset ip

```
ipconfig -release
ipconfig /release
```



```
ipconfig -renew
ipconfig /renew
```



### Reset TCP/IP Stack

```
netsh int ip reset
```



```
netsh int ipv4 set address name="Housing Static DNS" dhcp 
```





### Flush DNS

```
ipconfig -flushdns
ipconfig /flushdns
```



### Repair system files

```
sfc /scannow
```



# Powershell

## host info

```
get-wmiobject win32_computersystem
```

```
Get-WMIObject Win32_BIOS
```



```
Import-Module ActiveDirectory

get-adcomputer -Filter 'Name -like "ws-jo*"'
```







# startup

Windows + R

```
shell:startup
```

Copy the shortcuts at here