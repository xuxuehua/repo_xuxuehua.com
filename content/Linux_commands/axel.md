---
title: "axel"
date: 2018-11-16 10:42
---


[TOC]


# axel

A light download accelerator for Linux



## Installation

```
wget -c http://pkgs.repoforge.org/axel/axel-2.4-1.el5.rf.x86_64.rpm
rpm -ivh axel-2.4-1.el5.rf.x86_64.rpm
```



## SYNOPSIS

```
axel [OPTIONS] url1 [url2][url...]
```





## options

```
--max-speed=x , -s x         最高速度x
--num-connections=x , -n x   连接数x
--output=f , -o f            下载为本地文件f
--search[=x] , -S [x]        搜索镜像
--header=x , -H x            添加头文件字符串x（指定 HTTP header）
--user-agent=x , -U x        设置用户代理（指定 HTTP user agent）
--no-proxy ， -N             不使用代理服务器
--quiet ， -q                静默模式
--verbose ，-v               更多状态信息
--alternate ， -a            Alternate progress indicator
--help ，-h                  帮助
--version ，-V               版本信息
```



## example

如下载lnmp安装包指定10个线程，存到/tmp/：

```
axel -n 10 -o /tmp/ http://www.linuxde.net/lnmp.tar.gz
```