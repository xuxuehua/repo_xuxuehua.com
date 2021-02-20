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


## -C 请求压缩所有数据
请求压缩所有数据



## -D 动态转发

动态转发有点更加强劲的端口转发功能，即是无需固定指定被访问目标主机的端口号。这个端口号需要在本地通过协议指定，该协议就是简单、安全、实用的 [SOCKS](https://en.wikipedia.org/wiki/SOCKS) 协议。



```
ssh -D 50000 user@host1
```

> 这条命令创建了一个SOCKS代理，所以通过该SOCKS代理发出的数据包将经过`host1`转发出去。



```
ssh -i /Users/rxu/.ssh/rxuTestingKeyPair.pem -f -ND 8157 ec2-user@3.3.3.3
```




## -F 
指定ssh指令的配置文件


## -f 后台执行
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




## -N 不执行远程指令
不执行远程指令



## -o option 配置选项

指定配置选项

Can be used to give options in the format used in the configuration file.  This is useful for specifying options for which there is no separate command-line flag



### ServerAliveInterval

指定ssh 超时时间

```
ssh -o ServerAliveInterval=30 user@host
```



### StrictHostKeyChecking

Automatically accept keys

```
ssh -oStrictHostKeyChecking=no $h 
```





### PreferredAuthentications

Force SSH client to use password authentication instead of public key

```
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no user@host
```





### PubkeyAuthentication

Force SSH client to use password authentication instead of public key

```
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no user@host
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



#### connect

```
git clone https://github.com/ghostlyman/connect-proxy.git
cd connect 
make 
gcc connect.c -o connect 
cp connect /usr/local/bin
```



```
ssh -o "ProxyCommand connect -S 127.0.0.1:1080 %h %p" user@host
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



## -t tty

Force pseudo-tty allocation

This can be used to execute arbitrary screen-based programs on a remote machine, which can be very useful

Multiple -t options force tty allocation, even if ssh has no local tty



# banner

```
* This system is owned by XXX Inc.                 *
* If you are not authorized to access this system, exit *
* immediately. Unauthorized access to this system is    *
* forbidden by company policies, national, and          *
* international laws. Unauthorized users are subject to *
* criminal and civil penalties as well as company       *
* initiated disciplinary proceedings.                   *
*                                                       *
* By entry into this system you acknowledge that you    *
* are authorized access and the level of privilege you  *
* subsequently execute on this system. You further      *
* acknowledge that by entry into this system you        *
* expect no privacy from monitoring.
```






## -X 
开启X11转发功能


## -x 
关闭X11转发功能


## -y 
开启信任X11转发功能



## config 文件

```
Host *
    ServerAliveInterval 60
```

`Host *` #表示需要启用该规则的服务端（域名或ip）
`ServerAliveInterval 60` #表示没60秒去给服务端发起一次请求消息（这个设置好就行了）
`ServerAliveCountMax 3` #表示最大连续尝试连接次数（这个基本不用设置）



## authorized_keys

### enable prompt

```
no-port-forwarding,no-agent-forwarding,no-X11-forwarding,command="echo 'Please login as the user \"ec2-user\" rather than the user \"root\".';echo;sleep 10" ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCPcfG0A9OoLd+UGhLBHB3BwZ2W+a1SGYW0Z5dddm2AvfQ7kbKN7h4NkSO2sMJbNfb7rk0dDt1vqVgHSi4WvLB0A5 rxu_test_key
```



# example

## ssh 转发

```
ssh -i ~/.ssh/id_rsa -p $PORT  -CfNg -D 127.0.0.1:1083 3.3.3.3
```

