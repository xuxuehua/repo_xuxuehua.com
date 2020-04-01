---
title: "json"
date: 2018-08-24 12:31
---



[TOC]

# json

JavaScript Object Notation 对象标记，是一种轻量级的数据交换格式，基于ECMAScript的一个子集， 完全独立于编程语言的文本格式来存储和表示数据

字符串是json格式的字符串



## 类型转换

| Python 类型 | Json 类型 |
| :---------- | :-------- |
| True        | true      |
| False       | false     |
| None        | null      |
| str         | string    |
| int         | integer   |
| float       | float     |
| list        | array     |
| dict        | Object    |



## dump 编码并存入文件 

```
import json

person = [
    {
        'name': 'Rick',
        'age': 30
    },
    {
        'country': 'China',
        'Language': 'Python'
    }
]
json_str = json.dumps(person)

with open('person.json', 'w') as f:
    json.dump(person, f)
```



### 中文编码处理

```
import json

person = [
    {
        'name': 'Rick Xu',
        'age': 30
    },
    {
        'country': '中国',
        'Language': 'Python'
    }
]
json_str = json.dumps(person)

with open('person.json', 'w', encoding='utf-8') as f:
    json.dump(person, f, ensure_ascii=False)
```



## dumps 编码 序列化操作

将内从中的对象存储下来，变成字节格式

```
import json

person = [
    {
        'name': 'Rick',
        'age': 30
    },
    {
        'country': 'China',
        'Language': 'Python'
    }
]

print(json.dumps(person))

>>>
[{"age": 30, "name": "Rick"}, {"Language": "Python", "country": "China"}]
```



## load 解码从文件读取

```
import json

with open('person.json', 'r', encoding='utf-8') as f:
    person = json.load(f)
    print(person)
    
>>>
[{'name': 'Rick Xu', 'age': 30}, {'country': '中国', 'Language': 'Python'}]
```



## loads 解码 反序列化操作

将文件中的字节格式恢复称内存中的对象

```
import json

json_str = '[{"age": 30, "name": "Rick"}, {"Language": "Python", "country": "China"}]'

print(json.loads(json_str))

>>>
[{'name': 'Rick', 'age': 30}, {'Language': 'Python', 'country': 'China'}]
```





