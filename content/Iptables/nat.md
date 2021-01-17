---
title: "nat"
date: 2018-11-27 18:37
---


[TOC]



# NAT

NAT  Network Address Translation 



NAT 常用在网络层和传输层转换

proxy 在应用层实现





## DNAT

目标地址转换(PREROUTING) 

只是修改请求报文的目标地址 

主要用于发布内部服务器，让内网中的服务器在外网中可以被访问到，要定义在 PREROUTING 链



端口映射和负载均衡都是DNAT （LVS替代了iptables的负载均衡功能）



有个web服务器放在内网配置内网ip，前端有个防火墙配置公网ip，互联网上的访问者使用公网ip来访问这个网站，当访问的时候，客户端发出一个数据包。这个数据包的报头里边，目标地址写的是防火墙的公网ip，防火墙会把这个数据包的报头改写一次，将目标地址改写成web服务器的内网ip，然后再把这个数据包发送到内网的web服务器上。这样，数据包就穿透了防火墙，并从公网ip变成了一个对内网地址的访问了。即DNAT，基于目标的网络地址转换



```
# iptables -t nat -A POSTROUTING -d NAT 服务器上的某外网地址 -p 某协议 -dport 某端口 -j DNAT --to-destination 内网某服务器地址 [:PORT]
```



访问172.16.37.10的web服务需经过192.168.1.1

```
# iptables -t nat -A PREROUTING -d 192.168.1.1 -p tcp --dport 80 -j DNAT --to-destination 172.16.37.10
```



## SNAT

源地址转换(POSTROUTING, OUTPUT) 

只是修改请求报文的源地址

主要用于实现让内网客户端访问外部主机时使用，要定义在 POSTROUTING 链，也可以在 OUTPUT 使用。



比如，多个PC机使用ADSL路由器共享上网，每个PC机都配置了内网IP，PC机访问外部网络的时候，路由器将数据包的报头中的源地址替换成路由器的ip。当外部网络的服务器比如网站web服务器接到访问请求的时候，他的日志记录下来的是路由器的ip地址，而不是PC机的内网ip。这是因为，这个服务器收到的数据包的报头里边的“源地址”，已经被替换了。所以叫做snat，基于源地址的地址转换



```
iptables -t nat -A POSTROUTING -s 内网网络或主机地址 -j SNAT --to-source NAT 服务器上的某外网地址

# 另一个TARGET: 地址伪装，能自行判断该转为哪个源地址；
iptables -t nat -A POSTROUTING -s 内网网络或主机地址 -j MASQUERADE
```





让172.16.0.0/16网络都用192.168.1.1去访问外网

```
iptables -t nat -A POSTROUTING -s 172.16.0.0/16 -j SNAT --to-source 192.168.1.1
```



### MASQUERADE

地址伪装

-j MASQUERADE (除非外网地址是动态的，效率比SNAT要低)

```
iptables -t nat -A POSTROUTING -s 内网网络或主机地址 -j MASQUERADE
```





SNAT可以NAT成一个地址，也可以NAT成多个地址。但是，对于snat，不管是几个地址，必须明确的指定要snat的ip。



假如当前系统用的是ADSL动态拨号方式，那么每次拨号，出口ip192.168.5.3都会改变，而且改变的幅度很大，不一定是192.168.5.3到192.168.5.5范围内的地址
这个时候如果按照现在的方式来配置iptables就会出现问题了。因为每次拨号后，服务器地址都会变化，而iptables规则内的ip是不会随着自动变化的，每次地址变化后都必须手工修改一次iptables，把规则里边的固定ip改成新的ip。这样是非常不好用的。MASQUERADE就是针对这种场景而设计的，他的作用是，从服务器的网卡上，自动获取当前ip地址来做NAT。

```
iptables -t nat -A POSTROUTING -s 10.8.0.0/255.255.255.0 -o eth0 -j MASQUERADE
```



#### AWS SNAT

https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html

disable Source/Destination Check

```
# cat /etc/sysctl.d/10-nat-settings.conf 
#
# NAT AMI settings
#

net.ipv4.ip_forward = 1
net.ipv4.conf.eth0.send_redirects = 0
```



```
#!/bin/bash
# Configure the instance to run as a Port Address Translator (PAT) to provide
# Internet connectivity to private instances.

function log { logger -s -t "vpc" -- $1; }

function die {
    [ -n "$1" ] && log "$1"
    log "Configuration of PAT failed!"
    exit 1
}

# Sanitize PATH
export PATH="/usr/sbin:/sbin:/usr/bin:/bin"

log "Determining the MAC address on eth0..."
ETH0_MAC=$(cat /sys/class/net/eth0/address) ||
    die "Unable to determine MAC address on eth0."
log "Found MAC ${ETH0_MAC} for eth0."

# This script is intended to run only on a NAT instance for a VPC
# Check if the instance is a VPC instance by trying to retrieve vpc id
VPC_ID_URI="http://169.254.169.254/latest/meta-data/network/interfaces/macs/${ETH0_MAC}/vpc-id"

VPC_ID=$(curl --retry 3 --silent --fail ${VPC_ID_URI})
if [ $? -ne 0 ]; then
   log "The script is not running on a VPC instance. PAT may masquerade traffic for Internet hosts!"
fi

log "Enabling PAT..."
sysctl -q -w net.ipv4.ip_forward=1 net.ipv4.conf.eth0.send_redirects=0 &&
(
    iptables -t nat -C POSTROUTING -o eth0 -j MASQUERADE 2> /dev/null ||
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE ) ||
die

sysctl net.ipv4.ip_forward net.ipv4.conf.eth0.send_redirects | log
iptables -n -t nat -L POSTROUTING | log

log "Configuration of PAT complete."
exit 0
```





## FULL_NAT

全地址转换

```
echo 1 > /proc/sys/net/ipv4/ip_forward
echo "net.ipv4.ip_forward = 1" > /etc/sysctl.conf
sysctl -p
```



安装iptables，并添加规则

```
yum install -y iptables-services
```

开启端口的UDP转发

```
iptables -t nat -A PREROUTING -p udp --dport 中转端口 -j DNAT --to-destination 目的ip:目的端口
iptables -t nat -A POSTROUTING -p udp -d 目的ip --dport 目的端口 -j SNAT --to-source NAT内网ip
```

开启端口的TCP转发

```
iptables -t nat -A PREROUTING -p tcp --dport 中转端口 -j DNAT --to-destination 目的ip:目的端口
iptables -t nat -A POSTROUTING -p tcp -d 目的ip --dport 目的端口 -j SNAT --to-source NAT内网ip
```



## PNAT

Port NAT 端口转换



### 显式扩展

（使用额外的匹配机制）：必须指明由哪个模块进行的扩展，在iptables中使用-m选项可以完成此功能

```
-m EXTENSION --spe-opt

state: 状态扩展
     结合ip_conntract 追踪会话的状态
          NEW：新连接请求
          ESTABLISHED：已建立的连接
          INVALID：非法连接
          RELATED：相关联的
          -m state --state NEW, ESTABLISHED -j ACCEPT

          iptables -A INPUT -d 10.76.33.201 -p tcp -m state --state ESTABLISHED

     multiport: 离散的多端口匹配扩展
          --source-ports
          --destination-ports
     -m multiport --destination-ports 21,22,80 -j ACCEPT

     -m iprange
          --src-range
          --dst-range 

          -s, -d 
          -s IP, NET
                   10.76.0.0/16, 10.76.33.201-10.76.33.254

          iptables -A INPUT -p tcp -m iprange --src-range 10.76.33.201-10.76.33.254 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

      -m connlimit 连接数限制
            ! --connlimit-above n  (一般加上！)  
                  iptables -A INPUT -d 10.76.33.201 -p tcp --dport 80 -m connlimit ! --connlimit-above 2 -j ACCEPT

      -m limit
             --limit   RATE   速率
                  iptables -R INPUT 6 -d 10.76.33.201 -p icmp --icmp-type 8 -m limit --limit 5/minute -j ACCEPT
             --limit-burst   6   连接会被重置
                  iptables -R INPUT 5 -d 10.76.33.201 -p icmp --icmp-type 8 -m limit --limit 5/minute --limit-burst 6 -j ACCEPT

      -m string 
            --algo {bm|kmp}
            --string "STRING"
                    iptables -I OUTPUT -s 10.76.33.201 -m string --algo kmp --string "h7n9" -j REJECT 
```

​          





# example 

## ss relay

If you want your client connected to a Japan VPS, but you want a US IP.

```
Client <--> Japan VPS <--> US VPS
```

Easy version:

1. Setup ss server as usual on US VPS.

2. On Japan VPS, enable forwarding. Replace `US_VPS_IP` and `JAPAN_VPS_IP` with actual IP:

   ```
    sudo su - 
    echo 1 > /proc/sys/net/ipv4/ip_forward
    
    iptables -t nat -A PREROUTING -p tcp --dport 8388 -j DNAT --to-destination US_VPS_IP:8388
    iptables -t nat -A POSTROUTING -p tcp -d US_VPS_IP --dport 8388 -j SNAT --to-source JAPAN_VPS_IP
   ```

3. Set your server to JAPAN_VPS_IP:8388 on your client.



## Azure relay

```
iptables -t nat -A PREROUTING -p tcp --dport 8388 -j DNAT --to-destination SS_VPS_IP:8388
iptables -t nat -A PREROUTING -p udp --dport 8388 -j DNAT --to-destination SS_VPS_IP:8388
iptables -t nat -A POSTROUTING -p tcp -d SS_VPS_IP --dport 8388 -j SNAT --to-source Azure内部 IP 地址
iptables -t nat -A POSTROUTING -p udp -d SS_VPS_IP --dport 8388 -j SNAT --to-source Azure内部 IP 地址
```

> **Azure在外层有一层NAT**，所给虚拟机的IP虽然是公网IP，但是不是绑定在虚拟机网卡上的IP。Azure的防火墙在收到数据包后进行一次NAT，转发给内部虚拟机**。出去的数据包也经过一次NAT**，之后才进行发送。
> 虽然在出咱虚拟机网卡的包的目标地址被正确的修改，指向了SS-vps，但是源地址是该虚拟机的外网IP（Azure叫他：公用虚拟 IP (VIP)地址），这个包在经国Azure的外围防火墙的时候被丢弃，因为认为这个包的源IP不是内部的服务器的。