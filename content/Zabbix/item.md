---
title: "item"
date: 2019-03-02 20:27
---


[TOC]



# item



## 类型



### zabbix-agent

以agent为视角，分passive 和active 工作模式



### 网卡相关

```
net.if.in[if,<mode>]
```

> If: 接口， eth0
>
> mode: bytes, packets, errors, dropped



```
net.if.out[if,<mode>]
net.if.total[if,<mode>]
```



### 端口相关

```
net.tcp.listen[port]
net.tcp.port[<ip>,port]
net.tcp.service[service,<ip>,<port>]
net.udp.listen[port]
```



### 进程相关

```
kernel.maxfiles
kernel.maxproc
```





### CPU相关

```
system.cpu.intr
system.cpu.load[<cpu>,<mode>]
system.cpu.num[<type>]
system.cpu.switches
system.cpu.util[<cpu>,<type>,<mode>]
```



### 磁盘IO及系统相关

```
vfs.dev.read[<device>,<type>,<mode>]
vfs.dev.write[<device>,<type>,<mode>]
vfs.fs.inode[fs,<mode>]
```





