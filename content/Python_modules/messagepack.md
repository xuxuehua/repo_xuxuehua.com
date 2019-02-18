---
title: "MessagePack 序列化"
date: 2019-02-01 23:46
---


[TOC]

# MessagePack

基于二进制高效的对象序列化类库，可用于跨语言通信

像JSON那样，在多种语言之间交换结构对象

比JSON更快速轻巧

据称比Google Protocol Buffers快4倍

兼容json 和pickle

## Installation

```
pip install msgpack-python
```



## example

```
import msgpack
import json

d = {'person': [{'name': 'Rick', 'age': 30}, {'name': 'Sam', 'age': 10}], 'total': 2}
j = json.dumps(d)
m = msgpack.dumps(d)

print('len of json: %s, len of msgpack: %s' % (len(j), len(m)))
print('-'*8)
print(j.encode(), len(j.encode()))
print(m)

>>>
len of json: 81, len of msgpack: 47
--------
b'{"person": [{"name": "Rick", "age": 30}, {"name": "Sam", "age": 10}], "total": 2}' 81
b'\x82\xa6person\x92\x82\xa4name\xa4Rick\xa3age\x1e\x82\xa4name\xa3Sam\xa3age\n\xa5total\x02'
```

