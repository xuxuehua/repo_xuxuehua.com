---
title: "pip"
date: 2019-01-26 00:28
---


[TOC]



# pip





## Options



### freeze

```
pip freeze > requirement.txt
```



### install

```
pip install -r requirement.txt
```



## 本地源

```
vim ~/.pip/pip.conf

[global]
index-url=http://mirrors.aliyun.com/pypi/simple
trusted-host=mirrors.aliyun.com
```

