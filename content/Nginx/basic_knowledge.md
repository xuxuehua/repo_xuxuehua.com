---
title: "basic_knowledge"
date: 2018-11-20 12:34
---


[TOC]


# basic_knowledge



## 特点

Apache，一个进程，同一时间只会处理一个连接一个请求



* 高并发
* 可扩展性和高可用性
* 热部署： 不停止服务的情况下，部署配置
* BSD许可证，可以更改代码



## 组成

### 二进制可执行文件

各个模块源码编译出的一个文件



### nginx.conf 配置文件

控制nginx 行为



### access.log 

记录http请求信息



### error.log

定位问题





## 版本

nginx-1.14.0 

nginx-1.15.5 

> 偶数版本比较稳定 1.14





## 安装

### 包管理工具

安装rpm或deb会将模块安装上去



### 编译安装

```
elinks http://nginx.org/en/download.html
curl -O 'http://nginx.org/download/nginx-1.14.1.tar.gz'

tar -xzf nginx-1.14.1.tar.gz
```



#### 安装顺序

```
./configure # 配置
make # 编译
make install # 编译安装
```



#### 目录结构

./configure 执行之前

```
auto # 辅助conf 告诉nginx 支持哪些模块
CHANGES  # 变更信息
CHANGES.ru # 俄语变更
conf # 事例文件
configure # 编译前的配置 
contrib # 将配置文件带上颜色， cp -r contrib/vim/* ~/.vim
html # 标准的html文件，基本配置
LICENSE
man # 帮助文件
README
src # 源码
```



./configure 执行之后

```

objs/  # 中间文件 
├── autoconf.err
├── Makefile
├── ngx_auto_config.h
├── ngx_auto_headers.h
├── ngx_modules.c  #决定了变异后，哪些模块会编译进nginx
└── src # 编译后的中间文件会放到这里
    ├── core 
    ├── event
    │   └── modules
    ├── http
    │   ├── modules
    │   │   └── perl
    │   └── v2
    ├── mail
    ├── misc
    ├── os
    │   ├── unix
    │   └── win32
    └── stream
```



#### --prefix 

指定安装目录

```
--prefix=/home/user/nginx
```





## 配置语法

配置文件由指令与指令块构成

每条指令以；分号结尾，指令与参数间以空格符号分隔

指令块以｛｝大括号将多条指令组织在一起

使用#符号添加注释，提高可读性

include语句允许组合多个配置文件以提升可维护性

使用$符号使用变量 （变量由nginx框架提供）

部分指令的参数支持正则表达式



## 配置参数

### 时间的单位

```
ms -> milliseconds
s -> seconds
m -> minutes
h -> hours
d -> days
M -> months, 30 days
y -> years, 365 days
```



### 空间的单位

```
bytes -> bytes
k/K -> kilobytes
m/M -> megabytes
g/G -> gigabytes
```

 

## 配置指令块  

### http



### server 域名

#### listen 指令

仅在server context中使用

```
listen unix:/var/run/nginx.sock;
listen 127.0.0.1:8000;
listen 8000;
listen *:8000;
listen [::]:8000 ipv6only=on
listen [::];
```



#### server_name 指令

Context: http, server, location







指令后可以跟多个域名

```
server_name www.xurick.com
```



泛域名，仅支持最前或者最后

```
server_name *.xurick.com
```



正则表达式， 加～前缀

```
server_name www.xurick.com ~^www\d+\.xurick\.com$;
```



### upstream 上游模块



### location URL 路径





## Nginx命令行

### syntax

```
格式：nginx -s reload
```



帮助：-? -h
使用指定的配置文件：-c
指定配置指令：-g
指定运行目录：-p
发送信号：-s {stop立刻停止服务,quit优雅的停止服务,reload重载配置文件,reopen重新开始记录日志文件}
测试配置文件是否有语法错误：-t -T
打印nginx的版本信息、编译信息等：-v -V



### 热部署版本升级

```
kill -USR2 masterPid
kill -WINCH masterPid 切换到新的版本，老版本的进程不会退出，方便进行版本回退
```

版本回退

直接用kill -USR1来执行reload，不要用nginx -s reload，这样还是老的nginx worker起来



### 日志切割

```
mv access.log bak.log 
../sbin/nginx -s reopen
```

>  这里要用mv而不是cp，因为linux文件系统中，改名并不会影响已经打开文件的写入操作，内核inode不变，这样就不会出现丢日志了
>
> 如果用mv移走，其实还在写移动了目录的老文件，因为进程中的句柄没变。用reopen重新打开这个目录下的文件，因为文件已经不存在，所以会重新创建



常用cronjob 处理日志切割

```
0 0 1 * * root /usr/local/openresty/nginx/logs/rotate.sh
```



rotate.sh

```
#!/bin/bash

LOGS_PATH=/usr/local/openresty/nginx/logs/history
CUR_LOGS_PATH=/usr/local/openresty/nginx/logs
YESTERDAY=$(date -d "yesterday" +%Y-%m-%d)
mv ${CUR_LOGS_PATH}/access.log ${LOGS_PATH}/access_${YESTERDAY}.log
kill -USR1 $(cat /usr/local/openresty/nginx/logs/nginx.pid)
```



## 进程结构

### Master Process

Master Process 用于管理Worker Processes， Worker Processes 用于响应请求



所以会将CPU核数配置成Worker Processes 数量，或者绑定到指定的核上



### Worker processes



### Cache manager



### ngnix 信号

#### master 进程

通过CHLD 监控worker 进程， 当worker进程崩溃，可以被重新拉起



通过接受以下信号管理worker进程

```
TERM	立刻停止进程
INT  	立刻停止进程
QUIT	优雅停止进程
HUP		重载配置文件	
USR1	重新打开日志文件，做日志文件切割
USR2	做热部署时使用
WINCH	做热部署时使用
```



#### worker 进程

接受信号处理worker进程

```
TERM	立刻停止进程
QUIT	优雅停止进程
USR1	重新打开日志文件，做日志文件切割
WINCH	做热部署时使用
```



#### nginx 命令行

读取pid文件信息，可发送以下信号

```
reload	->	HUP
reopen	-> 	USR1
stop	-> 	TERM
quit	-> 	QUIT
```





## epoll

select和poll最大的问题是，每次都需要传递全部并发fd，而实际只有少量fd有数据需要处理，所以效率低下。而epoll通过epoll_ctl和epoll_wait分解了这个问题，效率大幅提高。