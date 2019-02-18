---
title: "set 类集合"
date: 2018-06-09 22:31
collection: 基本变量类型
---

[TOC]



# Set



## 定义

`Set`和`Map`类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在`Set`中，没有重复的key

`Set`与`Array`类似，但`Set`没有索引，可以迭代



## 创建 

```
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```



## 添加



### add(element)

通过`add(key)`方法可以添加

```
var s = new Set([1, 2, 3])
s.add(4);
s; // Set {1, 2, 3, 4}
s.add(4);
s; // 仍然是 Set {1, 2, 3, 4}
```



## 删除

### delete(key)

通过`delete(key)`方法可以删除

```
var s = new Set([1, 2, 3]);
s; // Set {1, 2, 3}
s.delete(3);
s; // Set {1, 2}
```

 



## 过滤重复元素

重复元素在`Set`中自动被过滤

```
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
```
