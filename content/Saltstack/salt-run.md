---
title: "salt-run"
date: 2019-06-19 16:40
---
[TOC]

# salt-run

Runners的功能，使用salt-run来运行，可以非常方便的在Master端执行相关的模块，获取minion端状态。



## manage 模块

```
[root@localhost salt]# salt-run manage.status
down:
up:
    - 192.168.1.11
```

