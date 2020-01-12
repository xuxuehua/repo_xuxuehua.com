---
title: "route"
date: 2020-01-08 23:10
---
[toc]



# route 

 route 命令可以查看 Linux 内核路由表

```
Destination     Gateway         Genmask Flags Metric Ref    Use Iface
192.168.0.0     *               255.255.255.0   U     0      0        0 eth0
169.254.0.0     *               255.255.0.0     U     0      0        0 eth0
default         192.168.0.1     0.0.0.0         UG    0      0        0 eth0
```



## 输出项解释

Destination	目标网段或者主机
Gateway	网关地址，”*” 表示目标是本主机所属的网络，不需要路由
Genmask	网络掩码

Flags	标记。一些可能的标记如下：

```
U Up表示此路由当前为启动状态。

H Host，表示此网关为一主机。

G Gateway，表示此网关为一路由器。

R Reinstate Route，使用动态路由重新初始化的路由。

D Dynamically,此路由是动态性地写入。

M Modified，此路由是由路由守护程序或导向器动态修改。

! 表示此路由当前为关闭状态。
```

Metric	路由距离，到达指定网络所需的中转数（linux 内核中没有使用）
Ref	路由项引用次数（linux 内核中没有使用）
Use	此路由项被路由软件查找的次数
Iface	该路由表项对应的输出接口



## 主机路由

主机路由是路由选择表中指向单个IP地址或主机名的路由记录。主机路由的Flags字段为H。例如，在下面的示例中，本地主机通过IP地址192.168.1.1的路由器到达IP地址为10.0.0.10的主机。

```
Destination    Gateway       Genmask Flags     Metric    Ref    Use    Iface
-----------    -------     -------            -----     ------    ---    ---    -----
10.0.0.10     192.168.1.1    255.255.255.255   UH       0    0      0    eth0
```



## 网络路由

网络路由是代表主机可以到达的网络。网络路由的Flags字段为N。例如，在下面的示例中，本地主机将发送到网络192.19.12的数据包转发到IP地址为192.168.1.1的路由器。

```
Destination    Gateway       Genmask Flags    Metric    Ref     Use    Iface
-----------    -------     -------         -----    -----   ---    ---    -----
192.19.12     192.168.1.1    255.255.255.0      UN      0       0     0    eth0

```



## 默认路由

当主机不能在路由表中查找到目标主机的IP地址或网络路由时，数据包就被发送到默认路由（默认网关）上。默认路由的Flags字段为G。例如，在下面的示例中，默认路由是IP地址为192.168.1.1的路由器。

```
Destination    Gateway       Genmask Flags     Metric    Ref    Use    Iface
-----------    -------     ------- -----      ------    ---    ---    -----
default       192.168.1.1     0.0.0.0    UG       0        0     0    eth0
```





# 命令操作

```
route  [add|del] [-net|-host] target [netmask Nm] [gw Gw] [[dev] If]
```

> add : 添加一条路由规则
> del : 删除一条路由规则
> -net : 目的地址是一个网络
> -host : 目的地址是一个主机
> target : 目的网络或主机
> netmask : 目的地址的网络掩码
> gw : 路由数据包通过的网关
> dev : 为路由指定的网络接口



## 添加到主机的路由

```
route add -host 192.168.1.2 dev eth0 
route add -host 10.20.30.148 gw 10.20.30.40     #添加到10.20.30.148的网管
```



## 添加到网络的路由

```
route add -net 10.20.30.40 netmask 255.255.255.248 eth0   #添加10.20.30.40的网络
route add -net 10.20.30.48 netmask 255.255.255.248 gw 10.20.30.41 #添加10.20.30.48的网络
route add -net 192.168.1.0/24 eth1
route add -net 224.0.0.0 netmask 240.0.0.0 dev eth0    #增加一条到达244.0.0.0的路由。
```

### reject 屏蔽

```
route add -net 224.0.0.0 netmask 240.0.0.0 reject     #增加一条屏蔽的路由，目的地址为224.x.x.x将被拒绝。
```



## 添加默认路由


```
route add default gw 192.168.1.1
```



## 删除路由

```
route del -host 192.168.1.2 dev eth0:0
route del -host 10.20.30.148 gw 10.20.30.40
route del -net 10.20.30.40 netmask 255.255.255.248 eth0
route del -net 10.20.30.48 netmask 255.255.255.248 gw 10.20.30.41
route del -net 192.168.1.0/24 eth1
route del default gw 192.168.1.1
route del -net 224.0.0.0 netmask 240.0.0.0 reject
```



# Linux 包转发

在 CentOS 中默认的内核配置已经包含了路由功能，但默认并没有在系统启动时启用此功能。开启 Linux 的路由功能可以通过调整内核的网络参数来实现

```
sysctl -w net.ipv4.ip_forward=1
```



用户还可以使用如下的命令查看当前系统是否支持包转发。

```
sysctl  net.ipv4.ip_forward
```