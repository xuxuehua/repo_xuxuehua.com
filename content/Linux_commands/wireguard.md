---
title: "wireguard"
date: 2018-11-30 21:31
---


[TOC]


# wireguard

# Installation 



## Ubuntu

```
sudo add-apt-repository ppa:wireguard/wireguard && 
sudo apt-get update && 
sudo apt-get install wireguard -y
```



## CentOS

```
sudo curl -Lo /etc/yum.repos.d/wireguard.repo https://copr.fedorainfracloud.org/coprs/jdoss/wireguard/repo/epel-7/jdoss-wireguard-epel-7.repo && 
sudo yum -y install epel-release && 
sudo yum -y install wireguard-dkms wireguard-tools
```



# 部署

## Ubuntu



### server 

需要配置Ip转发，默认ubuntu是关闭的

```
sudo vi /etc/sysctl.conf
添加：
net.ipv4.ip_forward=1
运行：
sudo sysctl -p
```



生成密钥

```
wg genkey | tee privatekey | wg pubkey > publickey
#查看私钥
cat privatekey
#查看公钥
cat publickey
```



配置文件设置/etc/wireguard/wg0.conf

```
[Interface]
Address = 192.168.2.1/24
SaveConfig = true
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE
ListenPort = 23334    #这里是公网机器打开的监听端口
PrivateKey = xxxxxxxxxxxxxx(用自己的公网机器上生成的私钥来替换)
[Peer]
PublicKey = xxxxxxxxxxxxxxx（用内网机器的公钥来替换）
AllowedIPs = 192.168.2.2/32 #把内网机的ip设置为192.168.2.2
Endpoint = 218.75.123.186:39055 # 者个部分由于内网机器没有公网IP可以百度看下自己的ip地址贴上，端口号可以先随意写，等内网机器连接上后端口会自动更新。
```

> 公网机器的ip配置成192.168.2.1。里面的PostUp，和PostDown分别来配置iptables转发规则
>
> 里面的ens3根据查看自己的ifconfig来修改
>
> AllowedIPs设置在发送时充当路由表，在接收时充当ACL。当对等方尝试将数据包发送到IP时，它将检查AllowedIPs，如果IP出现在列表中，它将通过WireGuard接口发送它。当它通过接口接收数据包时，它将再次检查AllowedIPs，如果数据包的源地址不在列表中，它将被丢弃。



wireGuard quick start后会默认所有的流量都走wg0

```
sudo wg-quick up wg0
```





* 配置路由问题

```
# ip route del default  #删除默认网关
# ip route add default dev wg0  #设置wg0为默认网关
# ip route add xx.xx.xx.xx/32 via yy.yy.yy.yy dev eth0   #把你公网ip的地址替换xx.xx.xx.xx ,用你的原来的网关替换yy.yy.yy.yy
```





* 配置路由表

中国Ip走国内路线，外国Ip走国外路线，在github上找到一个项目[地址](https://github.com/greensea/cnips) git地址[https://github.com/greensea/cnips.git](https://github.com/greensea/cnips.git)

```
gcc cnips.c -O2 -o cnipswget http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latestcat delegated-apnic-latest | ./cnips
```

* 写一个bash脚本

```
for net in `cat delegated-apnic-latest | ./cnips`do         route add -net $net gw 10.1.70.254  #这里的10.1.70.254用原来的网关进行替换done
```

* 配置DNS

配置好后还发现有问题，类似百度，B站等都有海外服务器用海外的DNS解析出来后都是海外地址，访问很慢。所以还需要自己搭建一个DNS。可以用docker现成项目来做[地址](https://github.com/jpillora/docker-dnsmasq)接下来还有国内常用域名git[地址](https://github.com/zizhengwu/GFW_DNS_SMART_RESOLVE)里面的dnsmasq.conf文件复制到Dns的配置里。





### client 

生成密钥

```
wg genkey | tee privatekey | wg pubkey > publickey
#查看私钥
cat privatekey
#查看公钥
cat publickey
```



配置文件设置/etc/wireguard/wg0.conf

```
[Interface]
PrivateKey = xxxxxxxxxxxxxx (用内网机器的私钥)
Address = 192.168.2.2/24    (和上面公网机器配置的保持一直)
[Peer]
PublicKey = xxxxxxxxxxxxxx (填写公网主机的公钥)
Endpoint = x.x.x.x:23334   (x.x.x.x用你的公网ip来填写，后面的公网机器的监听端口号)
AllowedIPs = 0.0.0.0/0, 192.168.2.1/32 （表示允许什么ip地址访问你的wireguard，前面的0.0.0.0/0很重要不添加的会不能访问一直显示网关不可达）
```



wireGuard quick start后会默认所有的流量都走wg0

```
sudo wg-quick up wg0
```





## wg-quick

wg-quick是用bash写的一个脚本



### server side

服务器端配的WireGuard配置文件是 /etc/wireguard/wg0.conf

```
[Interface]
Address = 192.168.10.1/24
MTU = 1500
ListenPort = 12345
PrivateKey = xxxx
 
[Peer]
PublicKey = xxxx
AllowedIPs = 192.168.10.40/32, 192.168.1.0/24
```

> **[Interface]** 这里定义的是虚拟网络设备的配置。
> **Address** 是网络设备的地址，IP地址。
> **MTU** 默认MTU是1420。改MTU的原因最后边会解释的。
> **ListenPort** 监听的UDP端口。
> **PrivateKey** 私钥，用 *wg genkey > private.key* 生成的文件内容。
>
> **[Peer]** 对端的地址，如果这里是C，对面就是S，如果这里是S，对面就是C。至于谁是C谁是S，鉴于是UDP，那姑且认为先发请求的是C。
> **PublicKey** 对端PrivateKey对应的私钥，用 *wg pubkey < private.key* 生成。
> **AllowedIPs** 这里解释一下，不管上边谁是C谁是S，想像一下传统的VPN需求，如果你要把流量全量转发到设备里，那直接配 0.0.0.0/0，而此时对端的设备就是S了，那对端的配置，把你的Interface.Address 和你本地的网段填入，不过后边这个理论上不是必须。
> **Endpoint** 上边的例子里没有，但是，作为C要连到S，那就需要告诉C连到哪个**IP:PORT**，至于S会自动生成对应C的Endpoint。



#### example

假定的网络环境如下：
服务器：IP 1.1.1.1，WireGuard内网 IP 192.168.10.1，公网环境
节点1: IP 2.2.2.2，WireGuard内网 IP 192.168.10.40，LAN IP 192.168.1.0/24
节点2: IP 3.3.3.3，WireGuard内网 IP 192.168.10.50，LAN IP 192.168.2.0/24

此处，服务器、节点1、节点2均有**公网IP**。

```
[Interface]
Address = 192.168.10.1/24
MTU = 1500
PrivateKey = xxx
PostUp = echo -n "hello" > /dev/udp/192.168.1.1/80; echo -n "hello" > /dev/udp/192.168.2.1/80
 
[Peer]
PublicKey = xxx
AllowedIPs = 192.168.10.40/32, 192.168.1.0/24
Endpoint = peer1:12345
 
[Peer]
PublicKey = xxx
AllowedIPs = 192.168.10.50/32, 192.168.2.0/24
Endpoint = peer2:12345
```



如果节点IP不固定，或者是没有公网IP，那就改为如下配置

```
[Interface]
Address = 192.168.10.1/24
MTU = 1500
PrivateKey = xxx
ListenPort = 54321
 
[Peer]
PublicKey = xxx
AllowedIPs = 192.168.10.40/32, 192.168.1.0/24
 
[Peer]
PublicKey = xxx
AllowedIPs = 192.168.10.50/32, 192.168.2.0/24
```





启用Wireguard

```
wg-quick up wg0
```



为了让网络正常工作，还需要在VPS的公网接口上启用NAT

```
iptables -F FORWARD
iptables -A FORWARD -j ACCEPT
 
iptables -A POSTROUTING -t nat -o eth0 -j MASQUERADE
```

修改 /etc/sysctl.conf，

```
启用 
net.ipv4.ip_forward=1
然后执行 
sysctl -p
```





# example

## traffic forwarding

要转发所有流量，只需将客户端上的AllowedIPs行更改为：

AllowedIPs = 0.0.0.0/0, ::/0

这是整个客户端配置：

```
[Interface]
Address = 192.168.2.2
PrivateKey = <client's privatekey>
ListenPort = 21841

[Peer]
PublicKey = <server's publickey>
Endpoint = <server's ip>:51820
AllowedIPs = 0.0.0.0/0, ::/0
```

　　这将使wg0接口负责路由所有IP地址（因此为0.0.0.0/0），并且应该通过服务器路由所有流量。 您可以通过加载我的IP网站之一来检查您的服务器的IP是否被检测到。

完成后，运行以下命令使目录和文件只能由管理员读取（它确实包含密钥）：

```
$ sudo chown -R root:root /etc/wireguard/
$ sudo chmod -R og-rwx /etc/wireguard/*
```

创建并保护文件后，如果操作系统使用systemd，则可以轻松设置WireGuard以在启动时初始化

```
$ sudo systemctl enable wg-quick@wg0.service
$ sudo systemctl start wg-quick@wg0.service
$ sudo systemctl stop wg-quick@wg0.service
```



## server

```
[Interface]
Address = 10.0.0.1/24
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = 51820
PrivateKey = server_private_key

[Peer]

PublicKey = client_public_key
AllowedIPs = 10.0.2.0/24
```

```
wg-quick down wg0 && wg-quick up wg0

GCE mtu 1420 
```



## client

```
[Interface]
PrivateKey = client_private_key
ListenPort = 51820
DNS = 8.8.8.8 
Address = 10.0.2.1/24

[Peer]
PublicKey = server_public_key
AllowedIPs = 0.0.0.0/0
Endpoint = 208.73.201.156:51820
PersistentKeepalive = 25
```

