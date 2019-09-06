---
title: "basic_knowledge"
date: 2019-08-30 09:58
---
[TOC]

# 模块



## 导出模块

`var ref = require('module_name');`

- 暴露变量也是`module.exports = variable;`

```
'use strict';

var s = 'Hello';

function greet(name) {
    console.log(s + ', ' + name + '!');
}

module.exports = greet;
```



## 调用模块

```
var greet = require('./hello');

var s = 'Rick';

console.log(greet(s))
```

> 这里调用的时候是相对目录



