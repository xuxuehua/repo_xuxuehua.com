---
title: "backup"
date: 2019-03-26 13:54
---


[TOC]



# 本地备份



## /path/to/repo.git 哑协议

无进度条

```
$ git clone --bare ~/coding/test/.git muted_obj.git
Cloning into bare repository 'muted_obj.git'...
done.
```



## file:///path/to/repo.git 智能协议

有进度条

```
$ git clone --bare file:///Users/xhxu/coding/test/.git smart_obj.git
Cloning into bare repository 'smart_obj.git'...
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (3/3), done.
```



# 远程备份



## http/https



## ssh