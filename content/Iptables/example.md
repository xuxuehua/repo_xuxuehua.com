---
title: "example"
date: 2018-11-27 18:45
---


[TOC]







# example

## 关闭所有规则链接

```
iptables -P INPUT DROP 

iptables -P OUTPUT DROP 

iptables -P FORWARD DROP 
```





## 更改默认策略

当我们使用-L选项验证当前规则是发现，所有的链旁边都有policy ACCEPT标注，这表明当前链的默认策略为ACCEPT：

```
# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination
ACCEPT tcp -- anywhere anywhere tcp dpt:ssh
DROP all -- anywhere anywhere

Chain FORWARD (policy ACCEPT)
target prot opt source destination

Chain OUTPUT (policy ACCEPT)
target prot opt source destination
```

这种情况下，如果没有明确添加DROP规则，那么默认情况下将采用ACCEPT策略进行过滤。除非：

为以上三个链单独添加DROP规则：

```
iptables -A INPUT -j DROP

iptables -A OUTPUT -j DROP

iptables -A FORWARD -j DROP
```

更改默认策略：

```
iptables -P INPUT DROP

iptables -P OUTPUT DROP

iptables -P FORWARD DROP
```





## 允许路由

如果本地主机有两块网卡，一块连接内网(eth0)，一块连接外网(eth1)，那么可以使用下面的规则将eth0的数据路由到eht1：

```
iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
```



## ICMP （PING）

开放172.16.37.1对本机172.16.37.10的ping响应，和ping请求；注：若默认INPUT/OUPUT为DROP，请求和响应同时开启才能ping通

```
iptables -A INPUT -s 172.16.37.1 -d 172.16.37.10 -p icmp --icmp-type 8 -j ACCEPT
iptables -A OUTPUT -s 172.16.37.10 -d 172.16.37.1 -p icmp --icmp-type 0 -j ACCEPT
iptables -P INPUT DROP
iptables -P OUTPUT DROP
```



### 允许来自外部的ping测试

```
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT
```



### 允许从本机ping外部主机

```
iptables -A OUTPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-reply -j ACCEPT
```



### 允许环回(loopback)访问

```
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
```



## SSH

### -m state 方法

默认链策略为DROP的情况下，进行防火墙设置

```
# 1.删除现有规则
iptables -F

# 2.配置默认链策略
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

# 3.允许接收远程主机的SSH请求
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

# 4.允许发送本地主机的SSH响应
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```

> -m state: 启用状态匹配模块（state matching module） 
>
> –-state: 状态匹配模块的参数。当SSH客户端第一个数据包到达服务器时，状态字段为NEW；建立连接后数据包的状态字段都是ESTABLISHED 
>
> –sport 22: sshd监听22端口，同时也通过该端口和客户端建立连接、传送数据。因此对于SSH服务器而言，源端口就是22 
>
> –dport 22: ssh客户端程序可以从本机的随机端口与SSH服务器的22端口建立连接。因此对于SSH客户端而言，目的端口就是22 



如果服务器也需要使用SSH连接其他远程主机，则还需要增加以下配置：

```
# 1.送出的数据包目的端口为22
iptables -A OUTPUT -o eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

# 2.接收的数据包源端口为22
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```



### 仅sshd

本机IP172.16.37.10，仅允许172.16.37.1访问sshd服务

```
iptables -A INPUT -s 172.16.37.1 -d 172.16.37.10 -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -s 172.16.37.10 -d 172.16.37.1 -p tcp --sport 22 -j ACCEPT
iptables -P INPUT DROP
iptables -P OUTPUT DROP
```



本例实现的规则将仅允许SSH数据包通过本地计算机，其他一切连接（包括ping）都将被拒绝。

```
# 1.清空所有iptables规则
iptables -F

# 2.接收目标端口为22的数据包
iptables -A INPUT -i eth0 -p tcp --dport 22 -j ACCEPT

# 3.拒绝所有其他数据包
iptables -A INPUT -j DROP
```



### SSH 字典攻击

```
# Prevent SSH Attack
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW -m recent --set --name SSH
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 3 --name SSH -j DROP
# Enable Normal SSH Connection
iptables -A INPUT -p tcp -m state --state NEW --dport 22 -j ACCEPT
```

### 允许所有SSH连接请求



本规则允许所有来自外部的SSH连接请求，也就是说，只允许**进入eth0接口，并且目的端口为22的数据包**

```
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```



### 允许从本地发起的SSH连接



本规则和上述规则有所不同，本规则意在允许本机发起SSH连接，上面的规则与此正好相反。

```
iptables -A OUTPUT -o eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```



### 仅允许来自指定网络的SSH连接请求



以下规则仅允许来自192.168.100.0/24的网络：

```
iptables -A INPUT -i eth0 -p tcp -s 192.168.100.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```

上例中，你也可以使用**-s 192.168.100.0/255.255.255.0**作为网络地址。当然使用上面的CIDR地址更容易让人明白。

### 仅允许从本地发起到指定网络的SSH连接请求



以下规则仅允许从本地主机连接到**192.168.100.0/24**的网络：

```
iptables -A OUTPUT -o eth0 -p tcp -d 192.168.100.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```



### 



## HTTP

### -m state 方法

```
# 1.删除现有规则
iptables -F

# 2.配置默认链策略
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

# 3.允许接收远程主机的HTTP请求
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT

# 4.允许发送本地主机的HTTP响应
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```



### 仅http

本机IP172.16.37.10，允许172.16.37.1访问httpd服务

```
iptables -A INPUT -s 172.16.37.1 -d 172.16.37.10 -p tcp --dport 80 -j ACCEPT
iptables -A OUTPUT -s 172.16.37.10 -d 172.16.37.1 -p tcp --sport 80 -j ACCEPT
iptables -P INPUT DROP
iptables -P OUTPUT DROP
```





### 允许HTTP/HTTPS连接请求



```
# 1.允许HTTP连接：80端口
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

# 2.允许HTTPS连接：443端口
iptables -A INPUT -i eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT
```





### 允许从本地发起HTTPS连接

本规则可以允许用户从本地主机发起HTTPS连接，从而访问Internet。

```
iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT
```

类似的，你可以设置允许HTTP协议（80端口）。











## 动态监控22， 80 ports

```
[root@localhost ~]# iptables -A INPUT -d 10.76.33.201 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
[root@localhost ~]# iptables -A OUTPUT -s 10.76.33.201 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
[root@localhost ~]# iptables -A INPUT -d 10.76.33.201 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
[root@localhost ~]# iptables -A OUTPUT -s 10.76.33.201 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```





## ssh，http，https 多端口

允许172.16.100.11对本机的ssh,http,https访问

```
iptables -I INPUT 1 -d 172.16.100.11 -p tcp -m multiport --dports 22,80,443 -j ACCEPT

iptables -I OUTPUT 1 -s 172.16.100.11 -p tcp -mmultiport --sports 22,80,443 -j ACCEPT
```







## VNC

```
# Open VNC for USER1
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 5800  -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 5900  -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 6000  -j ACCEPT
# Open VNC for USER2
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 5801  -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 5901  -j ACCEPT
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 6001  -j ACCEPT
```



## 自定义clean_in链，先经过clean_in 链处理

```
[root@localhost ~]# iptables -N clean_in
[root@localhost ~]# iptables -A clean_in -d 255.255.255.255 -p icmp -j DROP
[root@localhost ~]# iptables -A clean_in -d 10.76.33.201 -p icmp -j DROP
[root@localhost ~]# iptables -A clean_in -p tcp ! --syn -m state --state NEW -j DROP
[root@localhost ~]# iptables -A clean_in -p tcp --tcp-flags ALL NONE -j DROP
[root@localhost ~]# iptables -A clean_in -d 10.76.33.201 -j RETURN
[root@localhost ~]# iptables -I INPUT -j clean_in
[root@localhost ~]# iptables -L -n --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    clean_in   all  --  0.0.0.0/0            0.0.0.0/0           
2    REJECT     all  --  0.0.0.0/0            10.76.33.201        STRING match "h7n9" ALGO name kmp TO 65535 reject-with icmp-port-unreachable
3    ACCEPT     tcp  --  0.0.0.0/0            10.76.33.201        state RELATED,ESTABLISHED
4    ACCEPT     tcp  --  0.0.0.0/0            10.76.33.201        multiport dports 21,22,80 state NEW
5    ACCEPT     tcp  --  0.0.0.0/0            10.76.33.201        tcp dpt:21 state NEW
6    ACCEPT     tcp  --  0.0.0.0/0            10.76.33.201        state NEW,RELATED,ESTABLISHED
7    LOG        icmp --  0.0.0.0/0            10.76.33.201        icmp type 8 LOG flags 0 level 4 prefix `-- firewall log for icmp --'
8    ACCEPT     icmp --  0.0.0.0/0            10.76.33.201        icmp type 8 limit: avg 5/min burst 6

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  10.76.33.201         0.0.0.0/0           STRING match "h7n9" ALGO name kmp TO 65535 reject-with icmp-port-unreachable
2    ACCEPT     all  --  10.76.33.201         0.0.0.0/0           state RELATED,ESTABLISHED

Chain clean_in (1 references)
num  target     prot opt source               destination         
1    DROP       icmp --  0.0.0.0/0            255.255.255.255     
2    DROP       icmp --  0.0.0.0/0            10.76.33.201       
3    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0           tcp flags:!0x17/0x02 state NEW
4    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0           tcp flags:0x3F/0x00
5    RETURN     all  --  0.0.0.0/0            10.76.33.201       
```



## 利用iptables的recent模块抵御DOS攻击

```
iptables -I INPUT -p tcp --dport 22 -m connlimit --connlimit-above 3 -j DROP
iptables -I INPUT -p tcp --dport 22 -m state --state NEW -m recent --set --name SSH
iptables -I INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 300 --hitcount 3 --name SSH -j DROP
```

> 利用connlimit模块将单IP并发设置为3；会误杀使用NAT上网的用户，可以根据实际情况增大该值。 
>
> 利用recent和state模块限制单IP在 300s 内只能与本机建立3个新连接，被限制5mins后即可恢复访问。 
>
>
>
> 第二句：记录访问tcp 22端口的新连接，记录名称为SSH。--set 记录数据包的来源IP，如果IP已经存在将更新已经存在的条目 
>
> 第三句：SSH记录中的IP，300s内发起超过3次连接则拒绝此IP的连接 
>
> ​               --update 每次建立连接都更新列表 
>
> ​               --seconds 必须与--rcheck或者--update同时使用 
>
> ​               --hitcount 必须与--rcheck或者--update同时使用 



```
iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT
```





## 允许出站DNS连接

```
iptables -A OUTPUT -p udp -o eth0 --dport 53 -j ACCEPT
iptables -A INPUT -p udp -i eth0 --sport 53 -j ACCEPT
```



## 允许NIS连接



如果你在使用NIS管理你的用户账户，你需要允许NIS连接。即使你已允许SSH连接，你仍需允许NIS相关的ypbind连接，否则用户将无法登陆。NIS端口是动态的，当ypbind启动的时候，它会自动分配端口。因此，首先我们需要获取端口号，本例中使用的端口是853和850：

```
rpcinfo -p | grep ypbind
```

然后，允许连接到111端口的请求数据包，以及ypbind使用到的端口：

```
iptables -A INPUT -p tcp --dport 111 -j ACCEPT
iptables -A INPUT -p udp --dport 111 -j ACCEPT
iptables -A INPUT -p tcp --dport 853 -j ACCEPT
iptables -A INPUT -p udp --dport 853 -j ACCEPT
iptables -A INPUT -p tcp --dport 850 -j ACCEPT
iptables -A INPUT -p udp --dport 850 -j ACCEPT
```

以上做法在你重启系统后将失效，因为ypbind会重新指派端口。我们有两种解决方法：
1.为NIS使用静态IP地址
2.每次系统启动时调用脚本获得NIS相关端口，并根据上述iptables规则添加到filter表中去。



## 允许来自指定网络的rsync连接请求



你可能启用了rsync服务，但是又不想让rsync暴露在外，只希望能够从内部网络（192.168.101.0/24）访问即可：

```
iptables -A INPUT -i eth0 -p tcp -s 192.168.101.0/24 --dport 873 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 873 -m state --state ESTABLISHED -j ACCEPT
```



## 允许来自指定网络的MySQL连接请求



你可能启用了MySQL服务，但只希望DBA与相关开发人员能够从内部网络（192.168.100.0/24）直接登录数据库：

```
iptables -A INPUT -i eth0 -p tcp -s 192.168.100.0/24 --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT
```



## 允许Sendmail, Postfix邮件服务



邮件服务都使用了25端口，我们只需要允许来自25端口的连接请求即可。

```
iptables -A INPUT -i eth0 -p tcp --dport 25 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 25 -m state --state ESTABLISHED -j ACCEPT
```



## 允许IMAP与IMAPS



```
# IMAP：143
iptables -A INPUT -i eth0 -p tcp --dport 143 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 143 -m state --state ESTABLISHED -j ACCEPT

# IMAPS：993
iptables -A INPUT -i eth0 -p tcp --dport 993 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 993 -m state --state ESTABLISHED -j ACCEPT
```



## 允许POP3与POP3S



```
# POP3：110
iptables -A INPUT -i eth0 -p tcp --dport 110 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 110 -m state --state ESTABLISHED -j ACCEPT

# POP3S：995
iptables -A INPUT -i eth0 -p tcp --dport 995 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 995 -m state --state ESTABLISHED -j ACCEPT
```







## 负载平衡



可以利用iptables的**-m nth**扩展，及其参数（–counter 0 –every 3 –packet x），进行DNAT路由设置（-A PREROUTING -j DNAT –to-destination），从而将负载平均分配给3台服务器：

```
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 0 -j DNAT --to-destination 192.168.1.101:443
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 1 -j DNAT --to-destination 192.168.1.102:443
iptables -A PREROUTING -i eth0 -p tcp --dport 443 -m state --state NEW -m nth --counter 0 --every 3 --packet 2 -j DNAT --to-destination 192.168.1.103:443
```



## 记录丢弃的数据包



```
# 1.新建名为LOGGING的链
iptables -N LOGGING

# 2.将所有来自INPUT链中的数据包跳转到LOGGING链中
iptables -A INPUT -j LOGGING

# 3.指定自定义的日志前缀"IPTables Packet Dropped: "
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables Packet Dropped: " --log-level 7

# 4.丢弃这些数据包
iptables -A LOGGING -j DROP
```



## openvpn

这里以用于配置OpenVPN的NAT转发规则为例进行说明，希望读者能进一步了解规则设置的方法：

```
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o venet0 -j MASQUERADE
 
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i ! lo -d 127.0.0.0/8 -j REJECT
 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -j ACCEPT
 
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
iptables -A INPUT -p tcp -m state --state NEW --dport 22 -j ACCEPT
iptables -t nat -A PREROUTING -p udp -m udp --dport 53 -j DNAT --to-destination 8.8.8.8
 
iptables -A INPUT -p udp --dport 1194 -j ACCEPT
iptables -A INPUT -s 10.8.0.0/24 -p all -j ACCEPT
iptables -A FORWARD -d 10.8.0.0/24 -j ACCEPT
 
iptables -A INPUT -i tun+ -j ACCEPT
iptables -A FORWARD -i tun+ -j ACCEPT
 
iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7
 
iptables -A INPUT -j REJECT
iptables -A FORWARD -j REJECT
```





## 定义计时器

```
iptables -A ... && sleep 10 && iptables -F
```

