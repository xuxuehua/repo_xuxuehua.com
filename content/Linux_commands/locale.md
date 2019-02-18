---
title: "locale"
date: 2018-08-21 13:42
---

[TOC]



# locale 



## 本地语言



### 查看本地语言环境

```
(env3.5.3) xhxu-mac: xhxu$ locale
LANG="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_CTYPE="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_ALL=
```



#### 查看可选的语言环境

```
localectl list-locales
```



### 修改本地语言环境

`export LANG=en_US.UTF-8` 可以更改

```
localectl set-locale LANG=en_US.UTF-8
```



### 修改terminal 显示语言

```
[root@vps sucai]# locale -a | grep zh
zh_CN
zh_CN.gb18030
zh_CN.gb2312
zh_CN.gbk
zh_CN.utf8
zh_HK
zh_HK.big5hkscs
zh_HK.utf8
zh_SG
zh_SG.gb2312
zh_SG.gbk
zh_SG.utf8
zh_TW
zh_TW.big5
zh_TW.euctw
zh_TW.utf8

[root@vps sucai]# vim /etc/locale.conf
```



