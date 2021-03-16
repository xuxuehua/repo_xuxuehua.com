---
title: "pam"
date: 2019-05-24 17:56
---


[TOC]



# PAM

Pluggable Authentication Modules







## 配置文件

```
/etc/pam.d/service   配置文件
     type     control     module-path     [module-arguments] 非必须项

other 服务 is reserved for giving DEFAULT rules
     
required   需要检查，决定                [success=ok new_authtok_reqd=ok ignore=ignore default=bad]
requisite  决定权，不过就不需要检查了    [success=ok new_authtok_reqd=ok ignore=ignore default=die]
sufficient  决定权，通过就不用检查了       [success=done new_authtok_reqd=done default=ignore]
optional   可选的                                     [success=ok new_authtok_reqd=ok default=ignore]
include    由其他文件决定
```



# 模块



## pam_unix     

traditional password authentication 

​     Options 参数      

​     nullok     允许用空密码 

​     try_first_pass     使用第一次认证成功的密码，如果有密码弹出 

​     use_first_pass     使用第一次的密码 



## pam_permit  

允许访问 



## pam_deny  

拒绝访问 

​     通常用在other中 



## pam_cracklib  

​     依据字典检查密码 

​     Options 

​     minlen, difok, dcredit=N, ucredit=N, lcredit=N, ocredit=N, retry=N 



## pam_shells 

​     必须使用/etc/shells 下面存在的shell 



## pam_securetty 

​     限定root只能登陆的/etc/securetty的 



## pam_listfile  

​     根据某个文本文件进行验证 



## pam_limits 

即使是管理员也会受到限制

```
vim  /etc/security/limits.conf
<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - nofile - max number of open file descriptors
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
```



## pam_wheel.so     

限定哪些组的用户可以su到root用户 



## pam_time.so 

限定用户的登录时间 





# example

```
仅允许allowgrp组的用户登录实现方法
vim /etc/pam.d/system-auth-ac  
第二行添加
auth     required     pam_listfile.so item=group sense=allow file=/etc/pam_allowgroups
vim /etc/pam_allowgroups
添加
root
allowgrp

groupadd allowgrp 
usermod -a -G allowgrp fedora 


vim /etc/pam.d/su
auth     sufficient     pam_rootok.so
```

