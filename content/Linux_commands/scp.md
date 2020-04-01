---
title: "scp"
date: 2018-09-21 20:51
---


[TOC]

# scp

scp只是复制了文件的内容，并没复制文件的其他属性，如权限，时间戳等 

推荐使用rsync



## 命令格式

```
scp local_file remote_username@remote_ip:remote_folder 
或者 
scp local_file remote_username@remote_ip:remote_file 
```



## -4 IPv4

强制scp命令只使用IPv4寻址



## -6 IPv6

强制scp命令只使用IPv6寻址

## -B 免询问

使用批处理模式（传输过程中不询问传输口令或短语）



## -p (小写) 保留时间 

保留原文件的修改时间，访问时间和访问权限。



## -q 隐藏进度

不显示传输进度条。



## -r [递归]()

递归复制整个目录。

```
scp -r local_folder remote_username@remote_ip:remote_folder 
```



## -v verbose

详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。



## -c 以cipher传输

以cipher将数据传输进行加密，这个选项将直接传递给ssh。



## -F 指定SSH 配置

指定一个替代的ssh配置文件，此参数直接传递给ssh。



## -i 指定密钥

从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。

```
scp -i /var/spool/nagios/.ssh/id_rsa  ./retention.dat  monitor@nagios01.stg.xxxx.net:/tmp/ret.dat
```



## -l 限速

限定用户所能使用的带宽，以Kbit/s为单位。

## -o ssh_option

 如果习惯于使用ssh_config(5)中的参数传递方式，



## -P (大写) 指定端口

注意是大写的P, port是指定数据传输用到的端口号

```
scp -P 4588 remote@www.example.com:/usr/local/sin.sh /home/administrator
```

## -S 指定程序

指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。

