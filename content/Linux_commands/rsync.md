---
title: "rsync"
date: 2018-09-21 21:08
---


[TOC]


# rsync

remote sync。rsync是Linux系统下的文件同步和数据传输工具，它采用“rsync”算法，可以将一个客户机和远程文件服务器之间的文件同步，也可以在本地系统中将数据从一个分区备份到另一个分区上。与scp相比，它可以以最小的代价备份文件，只备份有差异的文件，这样每次备份就少了很多时间

如果rsync在备份过程中出现了数据传输中断，恢复后可以继续传输不一致的部分。

rsync可以执行完整备份或增量备份。它的主要特点有：
1.可以镜像保存整个目录树和文件系统；
2.可以很容易做到保持原来文件的权限、时间、软硬链接；无须特殊权限即可安装；
3.可以增量同步数据，文件传输效率高，因而同步时间短；
4.可以使用rcp、ssh等方式来传输文件，当然也可以通过直接的socket连接；
5.支持匿名传输，以方便进行网站镜象等；
6.加密传输数据，保证了数据的安全性；





## -a 归档拷贝

表示以递归方式传输文件，并保持所有文件属性，链接等,等于-rlptgoD



## -v verbose



## -e ssh tunnel



```
rsync -avz -e "ssh -i ./1 -p 54321 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" --progress localmedia ubuntu@destination_ip:/tmp 
```



## --exclude 排除文件或目录

```
rsync -av --exclude=ONE_DIRECTORY ./* ONE_DIRECTORY
```



## 本地到远程  PUSH

```
rsync -avH [ssh] /path/to/source user@des:/path/to/local  
```

```
rsync -av /opt/software/ hadoop102:/opt/software
```





## 远程到本地  PULL

```
rsync -avH [ssh] user@des:/path/to/source /path/to/local  
```



```
rsync -vare ssh jono@192.168.0.2:/home/jono/importantfiles/* /home/jono/backup/
```

> 备份了192.168.0.2地址上/home/jono/importantfiles/目录下的所有文件到当前机器上的/home/jono/backup目录下。