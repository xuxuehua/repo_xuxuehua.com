---
title: "collections"
date: 2019-06-20 10:23
---
[TOC]



# namedtuple

```
In [1]: from collections import namedtuple

In [2]: Student = namedtuple('Student' , ['name', 'age', 'gender'])

In [3]: s = Student('Rick', 18, 'male')

In [4]: s
Out[4]: Student(name='Rick', age=18, gender='male')

In [5]: s.name
Out[5]: 'Rick'

In [6]: s.age
Out[6]: 18
```



# Counter

```
In [34]: d
Out[34]:
{'0': 15,
 '1': 31,
 '2': 71,
 '3': 1,
 '4': 1,
 '5': 28,
 '6': 87,
 '7': 35,
 '8': 99,
 '9': 28}

In [35]: from collections import Counter

In [36]: c = Counter()

In [37]: c = Counter(d)

In [38]: c.most_common()
Out[38]:
[('8', 99),
 ('6', 87),
 ('2', 71),
 ('7', 35),
 ('1', 31),
 ('5', 28),
 ('9', 28),
 ('0', 15),
 ('3', 1),
 ('4', 1)]

In [39]: c.most_common(2)  # 传销频率最高
Out[39]: [('8', 99), ('6', 87)]
```



# defaultdict

defaultdict属于内建函数dict的一个子类, 提供一个default_factory 属性，该属性所指定的函数负责为不存在的key来生成value，即在没有key的情况下不会报错



构建的是一个类似*dictionary*的对象，其中*keys*的值，自行确定赋值，但是*values*的类型，是*function_factory*的类实例，而且具有默认值。

比如*default(int)*则创建一个类似dictionary对象，里面任何的*values*都是*int*的实例，而且就算是一个不存在的`key, d[key] `也有一个默认值，这个默认值是*int()*的默认值0.

```

In [41]: from collections import defaultdict                                                                                                                               
In [42]: d = defaultdict(list)                                                                                                                                             
In [43]: d['a']                                                                                                                                                            Out[43]: []

In [44]: d = defaultdict(int)                                                                                                                                              
In [45]: d['a']                                                                                                                                                            Out[45]: 0
```

 



## usage

defaultdict可以接受一个内建函数list作为参数

list()本身是内建函数，但是再经过更新后，python里面所有东西都是对象，所以list改编成了类，引入list的时候产生一个类的实例。

```
In [10]: from collections import defaultdict

In [11]: s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]

In [12]: d = defaultdict(list)

In [13]: d
Out[13]: defaultdict(list, {})

In [14]: for k, v in s:
    ...:     d[k].append(v)
    ...:

In [15]: list(d.items())
Out[15]: [('yellow', [1, 3]), ('blue', [2, 4]), ('red', [1])]
```





```
from collections import defaultdict
import re

f = open('ini.txt', mode='r', encoding='utf-8')
d = defaultdict

for line in f:
    for word in filter(lambda x: x, re.split(r'\s', line)):
        d[word] += 1

print(d)
```



# OrderedDict