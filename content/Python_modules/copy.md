---
title: "copy"
date: 2018-08-15 10:46
---

[TOC]

# copy 模块



浅拷贝是相当于创建一个全新的对象,但修改了新对象后依然能影响原对象,本质上还是对原对象的引用

深拷贝跟浅拷贝类似，深拷贝也会创建一个新的对象,并重新开辟一块新的内存已提供引用,再如何修改新对象也不会影响到原对象

```
>>> import copy
>>> origin = [1, 2, [3, 4]]
#origin 里边有三个元素：1， 2，[3, 4]
>>> cop1 = copy.copy(origin)
>>> cop2 = copy.deepcopy(origin)
>>> cop1 == cop2
True
>>> cop1 is cop2
False 
#cop1 和 cop2 看上去相同，但已不再是同一个object
>>> origin[2][0] = "hey!" 
>>> origin
[1, 2, ['hey!', 4]]
>>> cop1
[1, 2, ['hey!', 4]]
>>> cop2
[1, 2, [3, 4]]
#把origin内的子list [3, 4] 改掉了一个元素，观察 cop1 和 cop2
```

