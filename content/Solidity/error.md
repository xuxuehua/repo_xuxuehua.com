---
title: "error"
date: 2018-08-13 01:36
---

[TOC]



# 错误

## reverting

回滚机制（reverting）是为了确保transaction的原子性，主要分为require样式和assert样式。

 

### assert

assert用于检查程序运行条件是否满足（例如数组访问是否越界）



### require

require用于检查用户输入或外部调用返回值




### revert() 

执行失败需要给出异常提示，`throw`已经deprecated，应该采用`revert()`

如果采用`throw`抛出异常，所有的剩余`gas`都会消耗殆尽；而采用`revert()`的话，会回滚以前所有`transaction`的状态，同时交还没有消耗完的`gas`



