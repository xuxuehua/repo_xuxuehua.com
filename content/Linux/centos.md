---
title: "centos"
date: 2019-03-06 13:30
---


[TOC]



# CentOS

Community Enterprise Operation System



## Yum 

### 默认源

```
[root@localhost yum]# ls -la /etc/yum.repos.d/
total 60
drwxr-xr-x.  2 root root  270 Feb 23 00:01 .
drwxr-xr-x. 97 root root 8192 Feb 24 02:33 ..
-rw-r--r--.  1 root root 1664 Nov 23 08:16 CentOS-Base.repo
-rw-r--r--.  1 root root 1309 Nov 23 08:16 CentOS-CR.repo
-rw-r--r--.  1 root root  649 Nov 23 08:16 CentOS-Debuginfo.repo
-rw-r--r--.  1 root root  314 Nov 23 08:16 CentOS-fasttrack.repo
-rw-r--r--.  1 root root  630 Nov 23 08:16 CentOS-Media.repo
-rw-r--r--.  1 root root 1331 Nov 23 08:16 CentOS-Sources.repo
-rw-r--r--.  1 root root 5701 Nov 23 08:16 CentOS-Vault.repo
-rw-r--r--.  1 root root 2424 Dec 31 00:40 docker-ce.repo
-rw-r--r--.  1 root root  951 Oct  2  2017 epel.repo
-rw-r--r--.  1 root root 1050 Oct  2  2017 epel-testing.repo
-rw-r--r--.  1 root root  410 Oct  2 03:34 zabbix.repo
```



### 本地源

```
mount -t iso9660 /dev/sr0 /mnt

vim /etc/yum.repos.d/local.repo

[local]
name=local
baseurl=file:///mnt
enabled=1
gpgcheck=0
```



清除缓存

```
yum clean all
```

缓存源到本地

```
yum makecache
```





### Aliyun 源

```
wget -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```





### 163 源

```
wget -o /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS-Base-163.repo
```





### 修改源优先级

```
yum -y install yum-plugin-priorities
```



查看插件是否可用

0为禁用

1为启用

```
[root@localhost yum]# cat /etc/yum/pluginconf.d/priorities.conf
[main]
enabled = 1
```



修改本地优先级

```
vim /etc/yum.repos.d/local.repo

[local]
name=local
baseurl=file:///mnt
enabled=1
gpgcheck=0
priorities=1
```

> 数字越小优先级越高