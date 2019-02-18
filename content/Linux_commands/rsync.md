---
title: "rsync"
date: 2018-09-21 21:08
---


[TOC]


# rsync

Rsync命令是一个远程同步程序，与scp相比，它可以以最小的代价备份文件，只备份有差异的文件，这样每次备份就少了很多时间



## 本地到远程  PUSH

```
rsync -avH [ssh] /path/to/source user@des:/path/to/local  
```



## 远程到本地  PULL

```
rsync -avH [ssh] user@des:/path/to/source /path/to/local  
```

