---
title: "nfs"
date: 2021-01-30 17:47
---
[toc]





# nfs

http://nfs.sourceforge.net/nfs-howto/index.html



## installation



```
yum -y install nfs-utils rpcbind
```

nfs 的配置文件 /etc/expots
共享目录赋予权限：chmod 755 /home/data
vim /etc/exports
/home/data 192.168.0.0/24(rw,async,insecure,anonuid=1000,anongid=1000,no_root_squash)

* 使配置生效

```
exportfs -rv
```





* 启动nfs

```
systemctl enable rpcbind
systemctl start rpcbind
systemctl enable nfs-server
systemctl start nfs-server
```

确认NFS服务器启动成功：

```
rpcinfo -p
查看具体目录挂载权限
cat /var/lib/nfs/etab
```



## 客户端挂载

### linux

在从机上安装NFS 客户端

首先是安裝nfs，然后启动rpcbind服务

```
systemctl enable rpcbind.service

systemctl start rpcbind.service
```

注意：客户端不需要启动nfs服务

检查 NFS 服务器端是否有目录共享：

```
showmount -e nfs服务器的IP
showmount -e 192.168.0.63     
```

客户端挂载#开机自动挂载

```
vim /etc/fstab  
192.168.0.63:/home/data    /home/data     nfs4 rw,hard,intr,proto=tcp,port=2049,noauto    0  0
手工挂载：
mount -t nfs 192.168.0.63:/home/data /home/data
#查看是否挂载成功。
df -h 
NFS默认是用UDP协议，换成TCP协议达到稳定传输目的：
mount -t nfs 192.168.0.63:/home/data /home/data -o proto=tcp -o nolock
```



### Windows 

- Win7自带的NFS客户端可以在“控制面板”->“程序”->“

    WIndows

     

    功能”找到->nfs-安装。

    

- 由于自带的客户端功能少，缺少用户名映射，功能，所以必然会遇到权限的问题。所以需要自行配置权限问题

获取nfs server 用户web的gid和uid，并记录uid和gid，当前为：1000

打开注册表编辑器，找到HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default，添加两个REG_DWORD值，填上uid和gid（10进制）完成后重启电脑

注册表导出是如下格式 ：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default]
"AnonymousGid"=dword:000003e8
"AnonymousUid"=dword:000003e8
```

- 挂载到Z盘

```
mount -o anon mtype=soft lang=ansi  \\192.168.0.63\home\data\  Z:\
```

事项：一定要用软装载模式（mtype=soft），防止资源管理器停止响应，不能用utf-8

# 配置文件说明

## 192.168.1.0/24 

可以为一个网段，一个IP，也可以是域名，域名支持通配符 如: *.com

## rw

read-write，可读写；



## ro

read-only，只读；



## sync

文件同时写入硬盘和内存；



## async

文件暂存于内存，而不是直接写入内存；



## no_root_squash

NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，也拥有root权限。显然开启这项是不安全的。



## root_squash

NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，拥有匿名用户权限，通常他将使用nobody或nfsnobody身份

By default, any file request made by user root on the client machine is treated as if it is made by user nobody on the server. (Exactly which UID the request is mapped to depends on the UID of user "nobody" on the server, not the client.) 

If **no_root_squash** is selected, then root on the client machine will have the same level of access to the files on the system as root on the server. This can have serious security implications, although it may be necessary if you want to perform any administrative work on the client machine that involves the exported directories. You should not specify this option without a good reason.





## all_squash

不论NFS客户端连接服务端时使用什么用户，对服务端分享的目录来说都是拥有匿名用户权限；



## anonuid

匿名用户的UID值



## anongid

匿名用户的GID值。备注：其中anonuid=1000,anongid=1000,为此目录用户web的ID号,达到连接NFS用户权限一致。



## defaults 

使用默认的选项。默认选项为rw、suid、dev、exec、auto nouser与async。



## atime 

每次存取都更新inode的存取时间，默认设置，取消选项为noatime。



## noatime 

每次存取时不更新inode的存取时间。



## dev 

可读文件系统上的字符或块设备，取消选项为nodev。



## nodev 

不读文件系统上的字符或块设备。



## exec 

可执行二进制文件，取消选项为noexec。



## noexec 

无法执行二进制文件。



## auto 

必须在/etc/fstab文件中指定此选项。执行-a参数时，会加载设置为auto的设备，取消选取为noauto。



## noauto 

无法使用auto加载。



## suid 

启动set-user-identifier设置用户ID与set-group-identifer设置组ID设置位，取消选项为nosuid。



## nosuid 

关闭set-user-identifier设置用户ID与set-group-identifer设置组ID设置位。



## user 

普通用户可以执行加载操作。



## nouser 

普通用户无法执行加载操作，默认设置。



## remount 

重新加载设备。通常用于改变设备的设置状态



## rsize 

读取数据缓冲大小，默认设置1024。–影响性能



## wsize 

写入数据缓冲大小，默认设置1024



## fg 

以前台形式执行挂载操作，默认设置。在挂载失败时会影响正常操作响应



## bg 

以后台形式执行挂载操作



## hard

硬式挂载，默认设置。如果与服务器通讯失败，让试图访问它的操作被阻塞，直到服务器恢复为止



## soft 

软式挂载。服务器通讯失败，让试图访问它的操作失败，返回一条出错消息。这项功能对于避免进程挂在无关紧要的安装操作上来说非常有用



## retrans=n 

指定在以软方式安装的文件系统上，在返回一条出错消息之前重复发出请求的次数。



## nointr 

不允许用户中断，默认设置。



## intr 

允许用户中断被阻塞的操作并且让它们返回一条出错消息。



## timeo=n 

设置请求的超时时间以十分之一秒为单位。



## tcp 

传输默认使用udp,可能出现不稳定，使用proto=tcp更改传输协议。客户端参考mountproto=netid

