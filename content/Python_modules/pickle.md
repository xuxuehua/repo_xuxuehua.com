---
title: "pickle"
date: 2018-08-24 12:39
---

[TOC]

# pickle

仅支持Python所有的数据类型



## dump 

对象序列化为bytes对象

`pickle.dump(info, f)`

等同于

`f.write(pickle.dumps(info))`







## dumps 序列化

对象序列化到文件对象，就是存入文件中

将内存中的对象转换为文本流

```
import pickle

class Bird(object):
    have_feather = True
    way_of_reproduction = "egg"

summer = Bird()
# serialize object
picklestring = pickle.dumps(summer)
```





## load

对象反序列化，从文件中读取数据

`pickle.load(f)`

等同于

`pickle.loads(f.read())`



要从文本中读取文本，存储到字符串(文本文件的输入输出)。然后使用pickle.loads(str)的方法，将字符串转换称为对象。

```
import pickle

class Bird(object):
    have_feather = True
    way_of_reproduction = 'egg'

fn = 'a.pkl'
with open(fn, 'r') as f:
    summer = pickle.load(f)
```



## loads 反序列化

从bytes对象反序列化



