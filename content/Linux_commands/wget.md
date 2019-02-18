---
title: "wget"
date: 2018-10-30 02:44
---


[TOC]


# wget



## -c 断点续传

`--continue`

```
wget -c http://mirrors.163.com/ubuntu-releases/9.10/ubuntu-9.10-desktop-amd64.iso
```



## --limit-rate 限速

k = kb， m = mb

```
wget -c --limit-rate=300k http://mirrors.163.com/ubuntu-releases/9.10/ubuntu-9.10-desktop-amd64.iso 
```



## -O 重命名

`--output-document`

```
wget -c https://gist.github.com/chales/11359952/archive/25f48802442b7986070036d214a2a37b8486282d.zip -O db-connection-test.zip
```

可以执行shell重定向

```
wget -cO - https://gist.github.com/chales/11359952/archive/25f48802442b7986070036d214a2a37b8486282d.zip > db-connection-test.zip
```