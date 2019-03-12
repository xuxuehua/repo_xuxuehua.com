---
title: "sshd"
date: 2019-03-10 21:58
---


[TOC]



# sshd



## TCPKeepAlive

`TCPKeepAlive yes` 表示TCP保持连接不断开





## ClientAliveInterval

`ClientAliveInterval 300` #指定服务端向客户端请求消息的时间间隔，单位是秒，默认是0，不发送。设置个300表示5分钟发送一次（注意，这里是服务端主动发起），然后等待客户端响应，成功，则保持连接。



## ClientAliveCountMax

`ClientAliveCountMax 3` #指服务端发出请求后客户端无响应则自动断开的最大次数。使用默认给的3即可。
（注意：TCPKeepAlive必须打开，否则直接影响后面的设置。ClientAliveInterval设置的值要小于各层防火墙的最小值，不然，也就没用了。）