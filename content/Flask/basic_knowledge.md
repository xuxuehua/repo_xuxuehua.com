---
title: "basic_knowledge"
date: 2019-04-26 23:46
---


[TOC]



# Installation



```
pip install flask
```



# Hello World

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
    
if __name__ =="__main__":
    app.run(debug=True, port=8080)
```

> **name** 是Python中的特殊变量，如果文件作为主程序执行，那么`__name__`变量的值就是`__main__`，如果是被其他模块引入，那么`__name__`的值就是模块名称
>
> 默认情况下其地址是`localhost:5000`，在上面的代码中，我们使用关键字参数`port`将监听端口修改为8080



# 目录结构

```
├── app
│   ├── __init__.py
│   ├── admin
│   │   ├── __init__.py
│   │   ├── forms.py
│   │   └── views.py
│   ├── home
│   │   ├── __init__.py
│   │   ├── forms.py
│   │   └── views.py
│   ├── modules.py
│   ├── static
│   │   └── __init__.py
│   └── templates
│       ├── admin
│       └── home
├── demo.py
└── manage.py
```







# 蓝图

一种应用中或者跨应用制作应用组件和支持通用的模式

可以将不同的功能模块化，增强可读性，易于维护



## 定义蓝图

`app/admin/__init__.py`

```
from flask import Blueprint
admin = Blueprint("admin", __name__)
import views
```

## 注册蓝图

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

