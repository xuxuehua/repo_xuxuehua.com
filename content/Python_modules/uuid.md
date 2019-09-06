---
title: "uuid"
date: 2018-09-07 23:58
---


[TOC]


# uuid

UUID是128位的全局唯一标识符，通常由32字节的字符串表示。
它可以保证时间和空间的唯一性，也称为GUID，全称为：

UUID —— Universally Unique IDentifier      Python 中叫 UUID
GUID —— Globally Unique IDentifier          C#  中叫 GUID



它通过MAC地址、时间戳、命名空间、随机数、伪随机数来保证生成ID的唯一性, 可以用作数据库表的主键



## 实现方法

### uuid1() 基于时间戳 （常用）

由MAC地址、当前时间戳、随机数生成。可以保证全球范围内的唯一性，
但MAC的使用同时带来安全性问题，局域网中可以使用IP来代替MAC。

若在Global的分布式计算环境下，最好用uuid1





### uuid2() 基于分布式计算环境DCE

算法与uuid1相同，不同的是把时间戳的前4位置换为POSIX的UID。
实际中很少用到该方法。

Python中没有基于DCE的，所以uuid2可以忽略；





### uuid3() 基于名字的MD5散列值

通过计算名字和命名空间的MD5散列值得到，保证了同一命名空间中不同名字的唯一性，
和不同命名空间的唯一性，但同一命名空间的同一名字生成相同的uuid。    





### uuid4() 基于随机数

由伪随机数得到，有一定的重复概率，该概率可以计算出来。最好不用

第4个版本，不接收参数而生成随机UUID





### uuid5() 基于名字的SHA-1散列值

算法与uuid3相同，不同的是使用 Secure Hash Algorithm 1 算法

若有名字的唯一性要求，最好用uuid3或uuid5



## 使用

```
import uuid

name = 'test_name'

print(uuid.uuid1())
print(uuid.uuid3(uuid.NAMESPACE_DNS, name))
print(uuid.uuid4())
print(uuid.uuid5(uuid.NAMESPACE_DNS, name))

>>>
13533018-b4f5-11e8-8a2a-f45c89af0cc7
7e9fc176-3b19-394a-9530-83391161f8e9
b7ec2a9c-fee6-46a8-8fd7-bcf507b2440e
ad85ae8c-f638-56e0-b9fc-5d7a58009f62
```

