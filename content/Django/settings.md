---
title: "settings"
date: 2018-10-30 19:22
---


[TOC]


# settings

`mysite/settings.py`配置文件，这是整个Django项目的设置中心



## INSTALLED_APPS

顶部的`INSTALLED_APPS`设置项。它列出了所有的项目中被激活的Django应用（app）。



自动包含下列条目，它们都是Django自动生成的：

- django.contrib.admin：admin管理后台站点
- django.contrib.auth：身份认证系统
- django.contrib.contenttypes：内容类型框架
- django.contrib.sessions：会话框架
- django.contrib.messages：消息框架
- django.contrib.staticfiles：静态文件管理框架



## DATABASES	

Django默认使用SQLite数据库，因为Python源生支持SQLite数据库，所以你无须安装任何程序，就可以直接使用它。

* 基于pymysql操作Mysql数据库的例子

```
# mysite/settings.py

# Database
# https://docs.djangoproject.com/en/1.11/ref/settings/#databases

import pymysql         # 一定要添加这两行！通过pip install pymysql！
pymysql.install_as_MySQLdb()

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'HOST': '192.168.1.1',
        'USER': 'root',
        'PASSWORD': 'pwd',
        'PORT': '3306',
    }
}
```



### ENGINE（引擎）

可以是`django.db.backends.sqlite3`、`django.db.backends.postgresql`、`django.db.backends.mysql`、`django.db.backends.oracle`，当然其它的也行。



### NAME（名称）

类似Mysql数据库管理系统中用于保存项目内容的数据库的名字。如果你使用的是默认的SQLite，那么数据库将作为一个文件将存放在你的本地机器内，此时的NAME应该是这个文件的完整绝对路径包括文件名，默认值`os.path.join(BASE_DIR, ’db.sqlite3’)`，将把该文件储存在你的项目目录下。







## TIME_ZONE

国内所在的时区`Asia/Shanghai`