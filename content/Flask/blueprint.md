---
title: "blueprint"
date: 2019-09-03 08:32
---
[TOC]



# 蓝图



## 定义蓝图

`app/admin/__init__.py`

```
from flask import Blueprint
admin = Blueprint("admin", __name__)
import views
```

## 注册蓝图

使用Flask.register_blueprint方法进行注册, url_prefix 为该blueprint下所有的视图URL附加一个URL前缀

`app/__init__.py`

```
from admin import admin as admin_blueprint
app.register_blueprint(admin_blueprint, url_prefix="/admin")
```

## 调用蓝图

`app/admin/views.py`

```
from . import admin
@admin.route("/")
```



## 蓝图资源

定义独有的静态文件

static_url_path 参数可以为蓝图的static指定新的URL规则

```
auth_bp = Blueprint('auth', __name__, static_folder='static', static_url_path='/auth/static')
```





## 请求处理函数

使用before_request, after_request, teardown_request 会出发对应的请求函数处理









# 