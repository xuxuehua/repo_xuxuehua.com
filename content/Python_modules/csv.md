---
title: "csv"
date: 2018-09-26 13:35
---


[TOC]


# csv

“CSV”并不是一种单一的、定义明确的格式（尽管RFC 4180有一个被通常使用的定义）。因此在实践中，术语“CSV”泛指具有以下特征的任何文件：

1. [纯文本](https://zh.wikipedia.org/wiki/%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6)，使用某个字符集，比如[ASCII](https://zh.wikipedia.org/wiki/ASCII)、[Unicode](https://zh.wikipedia.org/wiki/Unicode)、[EBCDIC](https://zh.wikipedia.org/wiki/EBCDIC)或[GB2312](https://zh.wikipedia.org/wiki/GB2312)（简体中文环境）等；
2. 由[记录](https://zh.wikipedia.org/wiki/%E8%AE%B0%E5%BD%95)组成（典型的是每行一条记录）；
3. 每条记录被[分隔符](https://zh.wikipedia.org/w/index.php?title=%E5%88%86%E9%9A%94%E7%AC%A6&action=edit&redlink=1)分隔为[字段](https://zh.wikipedia.org/w/index.php?title=%E5%AD%97%E6%AE%B5&action=edit&redlink=1)（典型分隔符有逗号、分号或制表符；有时分隔符可以包括可选的空格）；
4. 每条记录都有同样的字段序列。



## 读取

### 列表方式 reader

reader是一个迭代器，返回为列表

next 方法可以跳过第一行

```
import csv

with open('example.csv', 'r') as f:
    reader = csv.reader(f) # reader是一个迭代器
    next(reader) # 跳过第一行
    for x in reader:
        print(x)
```



### 字典方式 DictReader

DictReader 是一个迭代器，返回为字典

DictReader不会包含标题的数据

```
import csv

with open('example.csv', 'r') as f:
    reader = csv.DictReader(f) # reader是一个迭代器
    for x in reader:
        print(x)
```





## 写入

### 列表方式 writer

```
import csv

headers = ['First Name', 'Last Name', 'Country']
values = [
    ('Rick', 'Xu', 'China'),
    ('Barack', 'Obama', 'USA')
]

with open('my.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(headers)
    writer.writerows(values)

```





### 字典方式 DictWriter

```
import csv

headers = ['First Name', 'Last Name', 'Country']
values = [
    {'First Name': 'Rick', 'Last Name': 'Xu', 'Country': 'China'},
    {'First Name': 'Barack', 'Last Name': 'Obama', 'Country': 'USA'}
]

with open('my.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.DictWriter(f, headers)
    writer.writeheader()
    writer.writerows(values)
```