---
title: "rsync"
date: 2018-09-21 21:08
---


[TOC]


# rsync

rsync命令是一个远程同步程序，与scp相比，它可以以最小的代价备份文件，只备份有差异的文件，这样每次备份就少了很多时间

rsync 是归档的拷贝，会复制所有的文件信息，包括权限时间戳等





## -a 归档拷贝



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