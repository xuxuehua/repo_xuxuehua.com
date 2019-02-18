---
title: "data_location"
date: 2018-09-02 16:29
---

[TOC]

# 数据存储 



## storage

storage：存储在EVM上永久空间



## memory

memory：存储在EVM中临时空间，`function`运行完之后就会释放



## calldata

calldata：类似`memory`，在`function`执行完后抹除





- 强制
  - 状态变量：`storage`
  - `function`输入参数：`calldata`
- 默认
  - `function`返回参数：`memory`
  - 本地变量：`storage`

1. 规则
   - 相同存储空间变量赋值
     - 传递`reference`：EVM上的内存地址
   - 不同存储空间变量赋值
     - 拷贝