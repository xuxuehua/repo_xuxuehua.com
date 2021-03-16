---
title: "auth"
date: 2021-02-24 09:22
---
[toc]



# Auth

App -> nsswitch -> resolve_lib

root -> nsswitch.conf -> passwd: files

'123456' -> nsswitch.conf -> shadow: files

auth: 123456 -> md5(salt) -> compare

认证本身也可以不用借助名称解析服务区查找用户原来存放的密码





```
/etc/nsswitch.conf
	passwd: files
	group: files
```



SUCCESS   service ok, found name

NOTFOUND   service ok, name not found 

UNAVAIL   service not available

TRYAGAIN   temporary service failure

getent 获取条目信息

   getent passwd    获取密码信息

​     getent passwd root



