---
title: "sshd"
date: 2019-03-10 21:58
---


[TOC]



# sshd



## TCPKeepAlive

`TCPKeepAlive yes` 表示TCP保持连接不断开





## ClientAliveInterval

`ClientAliveInterval 300` #指定服务端向客户端请求消息的时间间隔，单位是秒，默认是0，不发送。设置个300表示5分钟发送一次（注意，这里是服务端主动发起），然后等待客户端响应，成功，则保持连接。



## ClientAliveCountMax

`ClientAliveCountMax 3` #指服务端发出请求后客户端无响应则自动断开的最大次数。使用默认给的3即可。
（注意：TCPKeepAlive必须打开，否则直接影响后面的设置。ClientAliveInterval设置的值要小于各层防火墙的最小值，不然，也就没用了。）







# SSH限制IP和用戶



## hosts

除了可以禁止某个用户登录，我们还可以针对**固定的IP进行禁止登录**，linux 服务器通过设置**/etc/hosts.allow**和**/etc/hosts.deny**这个两个文件，hosts.allow许可大于hosts.deny可以限制或者允许某个或者某段IP地址远程 SSH 登录服务器，方法比较简单，且设置后立即生效，不需要重启SSHD服务，具体如下：

/etc/hosts.allow添加

```
sshd:192.168.0.1:allow  #允许 192.168.0.1 这个IP地址SSH登录
sshd:192.168.0.:allow #允许192.168.0.1/24这段IP地址的用户登录，多个网段可以以逗号隔开，比如192.168.0.,192.168.1.:allow
```

/etc/hosts.allow添加

```
sshd:ALL #允许全部的ssh登录 
```

hosts.allow和hosts.deny两个文件同时设置规则的时候，**hosts.allow文件中的规则优先级高**，按照此方法设置后服务器只允许192.168.0.1这个IP地址的SSH登录，其它的IP都会拒绝。

/etc/hosts.deny添加

```
sshd:ALL #拒绝全部IP
```



## sshd_config

在/etc/ssh/sshd_config配置文件中设置AllowUsers选项，（配置完成需要重启 SSHD 服务）格式如下：

```
AllowUsers    aliyun test@192.168.1.1            
# 允许 aliyun 和从 192.168.1.1 登录的 test 帐户通过 SSH 登录系统。
```

只拒绝指定用户进行登录（黑名单）：

在/etc/ssh/sshd_config配置文件中设置DenyUsers选项，（配置完成需要重启SSHD服务）格式如下：  

```
DenyUsers    zhangsan aliyun    #Linux系统账户        
# 拒绝 zhangsan、aliyun 帐户通过 SSH 登录系统
```