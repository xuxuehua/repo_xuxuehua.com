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



#### install for setup.py

```
pip install git+ssh://git@git.xurick.com/example/rx-plproxy.git@0.2.0 
```





## 本地源

```
vim ~/.pip/pip.conf

[global]
index-url=http://mirrors.aliyun.com/pypi/simple
trusted-host=mirrors.aliyun.com
```



### 包安装方法

这里安装flask-sqlalchemy

```
pip install -i http://mirrors.aliyun.com/pypi/simple --trusted-host mirrors.aliyun.com flask-sqlalchemy
```

