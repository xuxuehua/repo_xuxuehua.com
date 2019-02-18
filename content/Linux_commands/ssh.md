---
title: "ssh"
date: 2018-08-07 11:50
---

[TOC]



# ssh 

## -1 
强制使用ssh协议版本1


## -2 
强制使用ssh协议版本2


## -4 
强制使用IPv4地址


## -6 
强制使用IPv6地址


## -A 
开启认证代理连接转发功能


## -a 
关闭认证代理连接转发功能


## -b 
使用本机指定地址作为对应连接的源ip地址


## -C 
请求压缩所有数据



## -D 动态转发

动态转发有点更加强劲的端口转发功能，即是无需固定指定被访问目标主机的端口号。这个端口号需要在本地通过协议指定，该协议就是简单、安全、实用的 [SOCKS](https://en.wikipedia.org/wiki/SOCKS) 协议。



```
ssh -D 50000 user@host1
```

> 这条命令创建了一个SOCKS代理，所以通过该SOCKS代理发出的数据包将经过`host1`转发出去。






## -F 
指定ssh指令的配置文件


## -f 
后台执行ssh指令


## -g 
允许远程主机连接主机的转发端口


## -i 
指定身份文件


## -l 
指定连接远程服务器登录用户名



## -L 本地转发

本地转发，顾名思义就是把本地主机端口通过待登录主机端口转发到远程主机端口上去。

```
ssh -L 0.0.0.0:50000:host2:80 user@host1
```

>  这条命令将`host2`的80端口映射到本地的50000端口，前提是待登录主机`host1`上可以正常连接到`host2`的80端口。




## -N 
不执行远程指令



## -o option 配置选项

指定配置选项

Can be used to give options in the format used in the configuration file.  This is useful for specifying options for which there is no separate command-line flag



### StrictHostKeyChecking

Automatically accept keys

```
ssh -oStrictHostKeyChecking=no $h 
```







### ProxyCommand (Case sensitive)

```
ssh -o "ProxyCommand nc --proxy-type socks5 --proxy 127.0.0.1:9050 %h %p" <target_host>
```

```
ssh -o "ProxyCommand= nc -X 5 -x 127.0.0.1:9050 %h %p" user@host
```



To do it on a per-host basis, edit your ~/.ssh/config to look something like this:

```
host example.com
    user bar
    port 22
    ProxyCommand nc --proxy-type socks5 --proxy 127.0.0.1:9050 %h %p
```



Add this to your ssh config file (`~/.ssh/config`):

```
host *
     CheckHostIP  no
     Compression  yes
     Protocol     2
     ProxyCommand connect -4 -S localhost:9050 $(tor-resolve %h localhost:9050) 
%p
```




## -p 
指定远程服务器上的端口


## -q 
静默模式



## -R 远程转发

远程转发是指把登录主机所在网络中某个端口通过本地主机端口转发到远程主机上。

sshd_config里要打开`AllowTcpForwarding`选项，否则`-R`远程端口转发会失败。

默认转发到远程主机上的端口绑定的是`127.0.0.1`，如要绑定`0.0.0.0`需要打开sshd_config里的`GatewayPorts`选项。这个选项如果由于权限没法打开也有办法，可配合`ssh -L`将端口绑定到`0.0.0.0`



```
ssh -R 0.0.0.0:8080:host2:80 user@host1
```

> 这条命令将`host2`的80端口映射到待登录主机host1的8080端口，前提是本地主机可以正常连接`host2`的80端口。






## -X 
开启X11转发功能


## -x 
关闭X11转发功能


## -y 
开启信任X11转发功能


