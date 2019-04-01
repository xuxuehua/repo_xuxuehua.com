---
title: "sar"
date: 2018-11-27 01:07
---


[TOC]

# sar

System Activity Report 

sar 命令用于收集、汇报和保存系统活动信息



## Usage

```
sar [ options ] [ <interval> [ <count> ] ]
```



## option

### -b I/O 数据	

I/O and transfer rate statistics



### -B Paging 数据

Paging statistics



### -d 块设备状态	

Block device statistics





### -H 配置使用状态

Hugepages utilization statistics





### -I 中断信息

Interrupts statistics

	-I { <int> | SUM | ALL | XALL }




### -n 网络统计

```
-n { <keyword> [,...] | ALL }
```



	Network statistics
	Keywords are:
		
		DEV	Network interfaces
		EDEV	Network interfaces (errors)
		NFS	NFS client
		NFSD	NFS server
		SOCK	Sockets	(v4)
		IP	IP traffic	(v4)
		EIP	IP traffic	(v4) (errors)
		ICMP	ICMP traffic	(v4)
		EICMP	ICMP traffic	(v4) (errors)
		TCP	TCP traffic	(v4)
		ETCP	TCP traffic	(v4) (errors)
		UDP	UDP traffic	(v4)
		SOCK6	Sockets	(v6)
		IP6	IP traffic	(v6)
		EIP6	IP traffic	(v6) (errors)
		ICMP6	ICMP traffic	(v6)
		EICMP6	ICMP traffic	(v6) (errors)
		UDP6	UDP traffic	(v6)






```
sar -n DEV | more
```



显示 24 日的网络统计

```
# sar -n DEV -f /var/log/sa/sa24 | more
```



显示实时使用情况

```
# sar 4 5
```





### -m 硬件信息

```
-m { <keyword> [,...] | ALL }
```



	Power management statistics
	Keywords are:
	
		CPU	CPU instantaneous clock frequency
		FAN	Fans speed
		FREQ	CPU average clock frequency
		IN	Voltage inputs
		TEMP	Devices temperature
		USB	USB devices plugged into the system




### -q 队列

Queue length and load average statistics
	



### -r 内存使用率	

Memory utilization statistics
	



### -R 内存状态	

Memory statistics
	



### -S swap空间

Swap space utilization statistics
	

### -u [ ALL ] CPU使用率		

CPU utilization statistics
	



### -v 内核信息

Kernel table statistics
	

### -w 任务信息

Task creation and system switching statistics
	



### -W swapping 状态

Swapping statistics
	



### -y tty设备

TTY device statistics









