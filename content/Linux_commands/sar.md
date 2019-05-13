---
title: "sar"
date: 2018-11-27 01:07
---


[TOC]

# sar

System Activity Report 

sar 命令用于收集、汇报和保存系统活动信息, 不仅仅用于监控网络，还可以现实CPU，运行队列，磁盘I/O，分页交换区，内存，CPU中断等性能数据



![img](https://snag.gy/TZ3BPa.jpg)





## Installation

```
yum -y install sysstat
```



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

报告设备使用情况

```
[root@local ~]# sar -d 10 3
Linux 5.0.10-1.el7.elrepo.x86_64 (local.novalocal) 	05/03/2019 	_x86_64_	(2 CPU)

08:04:47 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
08:04:57 PM    dev8-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:04:57 PM  dev253-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:04:57 PM  dev253-1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

08:04:57 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
08:05:07 PM    dev8-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:05:07 PM  dev253-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:05:07 PM  dev253-1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

08:05:07 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
08:05:17 PM    dev8-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:05:17 PM  dev253-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
08:05:17 PM  dev253-1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

Average:          DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
Average:       dev8-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
Average:     dev253-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
Average:     dev253-1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
```

> tps:每秒从物理磁盘I/O的次数.多个逻辑请求会被合并为一个I/O磁盘请
> 求,一次传输的大小是不确定的.
> rd_sec/s:每秒读扇区的次数.
> wr_sec/s:每秒写扇区的次数.
> avgrq-sz:平均每次设备I/O操作的数据大小(扇区).
> avgqu-sz:磁盘请求队列的平均长度.
> await:从请求磁盘操作到系统完成处理,每次请求的平均消耗时间,包括请
> 求队列等待时间,单位是毫秒(1秒=1000毫秒).
> svctm:系统处理每次请求的平均时间,不包括在请求队列中消耗的时间.
> %util:I/O请求占CPU的百分比,比率越大,说明越饱和.
> avgqu-sz 的值较低时，设备的利用率较高。
> 当%util的值接近 1% 时，表示设备带宽已经占满。



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
		
		DEV	Network interfaces	显示网络接口信息
		EDEV	Network interfaces (errors)		显示关于网络错误的统计数据
		NFS	NFS client	统计活动的NFS客户端的信息
		NFSD	NFS server	统计NFS服务器信息
		SOCK	Sockets	(v4)	显示套接字信息
		ALL	Display all above 5 infos
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



#### IFACE 参数

```
rxpck/s		 每秒钟接收的数据包 
txpck/s    每秒钟发送的数据包
rxkB/s     每秒钟接收的字节数
txkB/s   	 每秒钟发送的字节数
rxcmp/s    每秒钟接收的压缩数据包
txcmp/s    每秒钟发送的压缩数据包
rxmcst/s	 每秒钟接收的多播数据包
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

监控内存分页

```
[root@local ~]#  sar -r 10 3
Linux 5.0.10-1.el7.elrepo.x86_64 (local.novalocal) 	05/03/2019 	_x86_64_	(2 CPU)

08:02:03 PM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
08:02:13 PM   1424472    616412     30.20     58692    401196    301860     11.82    335508    182568         4
08:02:23 PM   1423808    617076     30.24     58692    401204    301860     11.82    335820    182568        20
08:02:33 PM   1423984    616900     30.23     58704    401208    301860     11.82    335828    182568        24
Average:      1424088    616796     30.22     58696    401203    301860     11.82    335719    182568        16
```

> kbmemfree：这个值和free命令中的free值基本一致,所以它不包括buffer和cache的空间.
> kbmemused：这个值和free命令中的used值基本一致,所以它包括buffer和cache的空间.
> %memused：这个值是kbmemused和内存总量(不包括swap)的一个百分比.
> kbbuffers和kbcached：这两个值就是free命令中的buffer和cache.
> kbcommit：保证当前系统所需要的内存,即为了确保不溢出而需要的内存(RAM+swap).
> %commit：这个值是kbcommit与内存总量(包括swap)的一个百分比	



### -R 内存状态	

Memory statistics
	



### -S swap空间

Swap space utilization statistics
	

### -u [ ALL ] CPU使用率		

CPU utilization statistics

```
[root@localhost ~]# sar -u -o test 10 3
Linux 5.0.10-1.el7.elrepo.x86_64 (local.novalocal) 	05/03/2019 	_x86_64_	(2 CPU)

07:54:39 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
07:54:49 PM     all      0.00      0.00      0.05      0.00      0.00     99.95
07:54:59 PM     all      0.05      0.00      0.00      0.00      0.00     99.95
07:55:09 PM     all      0.00      0.00      0.05      0.05      0.05     99.85
Average:        all      0.02      0.00      0.03      0.02      0.02     99.92
```

> iowait	显示用于等待I/O操作占用CPU总时间的百分比， 若过高表示磁盘存在I/O瓶颈
>
> steal 	 管理程序hypervisor为另一个虚拟进程提供服务而等待虚拟CPU的百分比
>
> idle	   显示CPU空闲时间占用CPU总时间的百分比， 若过高但系统响应慢，可能是CPU等待分配内存，需要加大内存容量， 若持续低于1，则系统的CPU处理能力相对较低，表明系统中最需要解决CPU资源问题



查看生成的二进制文件

```
sar -u -f test
```



### -v 内核信息

Kernel table statistics

观察核心表的状态

```
[root@local ~]# sar -v 10 3
Linux 5.0.10-1.el7.elrepo.x86_64 (local.novalocal) 	05/03/2019 	_x86_64_	(2 CPU)

08:00:52 PM dentunusd   file-nr  inode-nr    pty-nr
08:01:02 PM    129226      1440     26630         1
08:01:12 PM    129226      1408     26597         1
08:01:22 PM    129226      1440     26630         1
Average:       129226      1429     26619         1
```

> dentunusd：目录高速缓存中未被使用的条目数量
> file-nr：文件句柄（ file handle）的使用数量
> inode-nr：索引节点句柄（ inode handle）的使用数量
> pty-nr：使用的pty数量

​	

### -w 任务信息

Task creation and system switching statistics
	



### -W swapping 状态

Swapping statistics
	



### -y tty设备

TTY device statistics









