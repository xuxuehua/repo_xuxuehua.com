---
title: "directories_and_files 目录和文件"
date: 2018-10-22 03:20
---


[TOC]

# 目录



## /etc

### /etc/sysconfig

修改hostname

```
修改/etc/sysconfig/network中的：

HOSTNAME

比如改成机器的IP：
NETWORKING=yes
HOSTNAME=10.20.150.92

GATEWAY=10.20.150.254



改完后运行/etc/rc.d/rc.sysinit
最后输入密码退出，不会重启，这个时候hostname 就修改完毕了
```



## /bin 

commands in this dir are all system installed user commands 

bin为binary的简写主要放置一些系统的必备执行档例如:cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar等



## /sbin

commands in this dir are all system installed super user commands 

主要放置一些系统管理的必备程式例如:cfdisk、dhcpcd、dump、e2fsck、fdisk、halt、ifconfig、ifup、 ifdown、init、insmod、lilo、lsmod、mke2fs、modprobe、quotacheck、reboot、rmmod、 runlevel、shutdown等。	





## /usr



### /usr/bin

user commands for applications 

是你在后期安装的一些软件的运行脚本。主要放置一些应用软体工具的必备执行档例如c++、g++、gcc、chdrv、diff、dig、du、eject、elm、free、gnome、 gzip、htpasswd、kfm、ktop、last、less、locale、m4、make、man、mcopy、ncftp、 newaliases、nslookup passwd、quota、smb、wget等。





### /usr/sbin

super user commands for applications 

放置一些用户安装的系统管理的必备程式例如:dhcpd、httpd、imap、in.*d、inetd、lpd、named、netconfig、nmbd、samba、sendmail、squid、swap、tcpd、tcpdump等





### /usr/X11R6/bin 

X application user commands 





### /usr/X11R6/sbin

X application super user commands 



## /proc

proc文件系统是一个伪文件系统，它只存在内存当中，而不占用外存空间。它以文件系统的方式为访问系统内核数据的操作提供接口。用户和应用程序可以通过proc得到系统的信息，并可以改变内核的某些参数。由于系统的信息，如进程，是动态改变的，所以用户或应用程序读取proc文件时，proc文件系统是动态从系统内核读出所需信息并提交的。



随机字符串

```
cat /proc/sys/kernel/random/uuid  | cut -c 1-8
1f842195
```



### 目录结构

并不是所有这些目录在你的系统中都有，这取决于你的内核配置和装载的模块。

另外，在/proc下还有三个很重要的目录：net，scsi和sys。Sys目录是可写的，可以通过它来访问或修改内核的参数（见下一部分），而net和scsi则依赖于内核配置。例如，如果系统不支持scsi，则scsi目录不存在。



```
/proc/1 
关于进程1的信息目录。每个进程在/proc 下有一个名为其进程号的目录。 

/proc/cpuinfo 
处理器信息，如类型、制造商、型号和性能。 

/proc/devices 
当前运行的核心配置的设备驱动的列表。 

/proc/dma 
显示当前使用的DMA通道。 

/proc/filesystems 
核心配置的文件系统。 

/proc/interrupts 
显示使用的中断，and how many of each there have been. 

/proc/ioports 
当前使用的I/O端口。 

/proc/kcore 
系统物理内存映象。与物理内存大小完全一样，但不实际占用这么多内存；it is generated on the fly as programs access it. (记住：除非你把它拷贝到什么地方，/proc 下没有任何东西占用任何磁盘空间。) 

/proc/kmsg 
核心输出的消息。也被送到syslog 。 

/proc/ksyms 
核心符号表。 

/proc/loadavg 
系统"平均负载"；3个没有意义的指示器指出系统当前的工作量。 

/proc/meminfo 
存储器使用信息，包括物理内存和swap。 

/proc/modules 
当前加载了哪些核心模块。 

/proc/net 
网络协议状态信息。 

/proc/self 
到查看/proc 的程序的进程目录的符号连接。当2个进程查看/proc 时，是不同的连接。这主要便于程序得到它自己的进程目录。 

/proc/stat 
系统的不同状态，such as the number of page faults since the system was booted. 

/proc/uptime 
系统启动的时间长度。 

/proc/version 
内核版本
```



### 进程目录

还有的是一些以数字命名的目录，它们是进程目录。系统中当前运行的每一个进程都有对应的一个目录在/proc下，以进程的PID号为目录名，它们是读取进程信息的接口。而self目录则是读取进程本身的信息接口，是一个link。Proc文件系统的名字就是由之而起。

```
Cmdline　命令行参数
Environ　环境变量值
Fd　一个包含所有文件描述符的目录
Mem　进程的内存被利用情况
Stat　进程状态
Status　Process status in human readable form
Cwd　当前工作目录的链接
Exe　Link to the executable of this process
Maps　内存印象
Statm　进程内存状态信息
Root　链接此进程的root目录
```



### /proc/sys 内核参数

在/proc文件系统中有一个有趣的目录：/proc/sys。它不仅提供了内核信息，而且可以通过它修改内核参数，来优化你的系统。但是你必须很小心，因为可能会造成系统崩溃。



改变内核的参数，只要用vi编辑或echo参数重定向到文件 中即可。

```
# cat /proc/sys/fs/file-max
4096
# echo 8192>; /proc/sys/fs/file-max
# cat /proc/sys/fs/file-max
8192
```



#### /proc/sys/kernel

修改hostname

```
sudo echo [my_host_name] > /proc/sys/kernel/hostname
```





## /var/log/

日志相关信息









# 文件



## libc.so.6

/lib64/libc.so.6 是重要依赖的库文件，删除后很多命令无法使用，包括关机都无法执行。

重启会卡在登录界面。

需要进入救援模式来恢复





## /var/log/lastlog

最近登录成功事件



## /var/log/utmp

当前机器登录的全部用户的日志



## /var/log/wtmp

系统创建以来登录过的用户

