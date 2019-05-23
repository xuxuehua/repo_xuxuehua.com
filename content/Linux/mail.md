---
title: "mail"
date: 2019-05-24 01:03
---


[TOC]

# 相关协议



## SMTP

Simple Mail Transfer Protocol 

smtp协议只负责邮件从客户端发生到服务器端



## ESMTP

Extended SMTP  





## POP3

Post Office Protocol version 3 





## IMAP4 

Internet Mail Access Protocol version 4 

功能比POP3功能强大，但是占用资源比较大 





## UUCP

Unix to Unix CoPy 

Unix主机复制文件的协议 







# 分类

![img](https://snag.gy/AFGzxC.jpg)





## MUA 

Mail User Agent 提供用户编写邮件 

```
 Outlook 
     Foxmail
     Thunderbird
     Evolution
     mutt： 文件界面的
```



## MTA 

Mail Transfer Agent  邮件传输代理

```
		 sendmail， UUCP
		 sendmail 是单体结构，会用到SUID， 配置文件语法（m4编写）
		 qmail， 性能超强，但是已不维护
     postfix：新贵， 模块化设计，安全，跟sendmail兼容很好，效率高
     exim： MTA， 
     Exchange：Windows， 异步消息协作平台， 必须要和AD整合使用
```







## MDA 

Mail Delivery Agent   邮件投递代理

```
procmail
maildrop
```



## MRA

Mail Retrieval Agent 邮件检索代理， 从邮件服务器的邮箱中取到，并传递给用户的过程 

```
实现imap4， pop3
     cyrus-imap
     dovecot
```

