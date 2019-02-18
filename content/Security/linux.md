---
title: "linux"
date: 2019-02-03 22:21
---


[TOC]



# Linux 



## 删减系统登录欢迎信息

为了保证系统的安全，可以修改或删除某些系统文件，需要修改或删除的文件有4个，分别是/etc/issue、/etc/issue.net、/etc/redhat-release和/etc/motd



/etc/os-release 记录系统版本



/etc/redhat-release文件也记录了操作系统的名称和版本号，为了安全起见，可以将此文件中的内容删除。