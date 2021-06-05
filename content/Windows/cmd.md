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







# bootsect (windows 10 booting)

Here's how I solved it:

1. I [burned a Windows 10 CD](http://www.howtogeek.com/131907/how-to-create-and-use-a-recovery-drive-or-system-repair-disc-in-windows-8/), entered troubleshooting, and from there I entered Windows Console.
2. Go to `Disk:\Windows\System32`, Then I typed in: `bootsect /nt60 drive_letter: /mbr` (replace drive_letter with your letter. for example, for me it was `C: /mbr`).
3. And it finally worked.







You are going to use the bootrec.exe tool to repair the corrupt MBR. Bootrec has a range of commands designed to recover the boot process from issues and is already on your Windows 10 system as part of the base installation.

![image-20210605005530879](/Users/rxu/Library/Application Support/typora-user-images/image-20210605005530879.png)

Type **bootrec.exe /fixmbr** and press Enter. Then type **bootrec.exe /fixboot** and press Enter. You should see **The operation completed successfully** underneath each command. If you don't see the operation completion message and instead receive an error, enter **bootrec.exe /rebuildbcd** and press Enter. The "rebuildbcd" command attempts to rebuild your system Boot Configuration Data (BCD).

![image-20210605005539756](/Users/rxu/Library/Application Support/typora-user-images/image-20210605005539756.png)

Unfortunately, this doesn't always work the first time around. In this case, Microsoft suggests exporting the BCD store (the place your boot data is kept) and completely rebuilding from scratch. Sounds scary, but it only takes a short moment.



Appendix

https://www.makeuseof.com/tag/fix-mbr-windows-guide/