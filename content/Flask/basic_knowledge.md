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



## 自动刷新

将debug=True 开启即可





# 视图函数注册



## 装饰器方法

常用方法

```
from flask import Flask

app = Flask(__name__)


@app.route('/hello/')
def hello():
    return 'Hello, Rick'

app.run(debug=True)
```



## add_url_url

在使用基于类的视图，即插式图，需要使用该方法

```
from flask import Flask

app = Flask(__name__)

def hello():
    return 'Hello, Rick'


app.add_url_rule('/hello/', view_func=hello)

app.run(debug=True)



```





# response 对象

response对象所返回的结果取决于`content-type` 所定义的value

```
from flask import Flask, make_response


app = Flask(__name__)

app.config.from_object('config')


@app.route('/hello/')
def hello():
    headers = {
        'content-type': 'text/plain',
        'location': 'http://www.bing.com'
    }
    # response = make_response('<html></html>', 301)
    # response.headers = headers
    return '<html></html>', 301, headers


app.add_url_rule('/hello/', view_func=hello)

if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=app.config['DEBUG'])
```







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

不建议将其作为试图函数的拆分

蓝图不能独立存在，需要插入到app的核心对象里面



![img](https://snag.gy/2kzj6W.jpg)







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







# 前端布局

## 静态文件引入

```
{{ url_for('static', filename='FILE_PATH') }}
```



## 定义路由

```
{{ url_for('MODULE_NAME.VIEW_NAME', VARIABLE=PARAMETER) }}
```



## 定义数据块

```
{% block BLOCK_NAME %}
INFO
{% endblock %}
```

