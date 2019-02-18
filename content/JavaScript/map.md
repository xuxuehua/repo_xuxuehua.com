---
title: "G_map 类字典"
date: 2018-06-09 22:28
---

[TOC]



# Map



## 定义

* 默认JS对象的key必须是字符串string，map允许Number或者其他数据类型作为键

```
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
```



## 初始化

* 初始化`Map`需要一个二维数组，或者直接初始化一个空`Map`

```
var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```

