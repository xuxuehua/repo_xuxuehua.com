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





## 开启多线程

```
app.run(threaded=True)
```



# 工作原理

![img](https://snag.gy/JNTclY.jpg)



当一个请求进来，先检查`_app_ctx_stack` 的堆栈中是否为空，若没有，那么将实例化 的`AppContext` 推入到栈中，然后 实例化一个Request Context，封装的信息在Request中，然后将Request Context推入到LocalStack中，LocalStack实例化之后，存储到`_request_ctx_stack` 中

在使用`current_app(Local Proxy)` 和 `request(Local Proxy)` 其实就是间接的操作两个栈顶的元素，也就是这两个上下文



AppContext 将Flask作为核心对象保存起来，RequestContext 把Request对象封装并保存了起来 



current_app指向的是LocalStack.top 栈顶元素的一个属性也就是 AppContext top.app = Flask 的核心对象

request是指向LocalStack.top 栈顶元素下面的Request请求对象,即 RequestContext top.resquest = Request

 

## LocalStack原理

Local是用字典的方式实现线程隔离，访问Local通常使用.点来访问下面的属性

LocalStack封装了Local对象，把Local对象作为一个属性，从而实现了线程隔离的栈结构，访问LocalStack需要使用指定的方法push，pop，top



```
from werkzeug.local import LocalStack

s = LocalStack()
s.push(1)
print(s.top)
print(s.top)
print(s.pop())
print(s.top)

s.push(1)
s.push(2)
print(s.top)
print(s.top)
print(s.pop())
print(s.top)

>>>
1
1
1
None
2
2
2
1
```



## 线程隔离

使用线程隔离的意义在于，使当前线程能够正确引用到他自己所创建的对象，而不是引用到其他线程所创建的对象

使用线程ID作为字典的Key来实现线程隔离





```
import threading, time
from werkzeug.local import LocalStack

my_stack = LocalStack()
my_stack.push(1)
print('Main thread with push value:', str(my_stack.top))


def worker():
    "New thread"
    print('New thread before push with value:', str(my_stack.top))
    my_stack.push(2)
    print('New thread after push with value:', str(my_stack.top))


new_t = threading.Thread(target=worker, name='rick_thread')
new_t.start()
time.sleep(1)
print('Eventually, main thread value:', str(my_stack.top))

>>>
Main thread with push value: 1
New thread before push with value: None
New thread after push with value: 2
Eventually, main thread value: 1
```











# Flask 源码结构

```
├── __init__.py
├── __main__.py
├── _compat.py
├── app.py
├── blueprints.py
├── cli.py
├── config.py
├── ctx.py
├── debughelpers.py
├── globals.py
├── helpers.py
├── json
│   ├── __init__.py
│   └── tag.py
├── logging.py
├── sessions.py
├── signals.py
├── templating.py
├── testing.py
├── views.py
└── wrappers.py
```





## app.py

含有核心实例化的Flask类



### Flask

保存配置文件信息， 注册路由，以及试图函数的功能





## ctx.py



### AppContext 

应用上下文， 对Flask 核心对象的封装

self.app = app 即封装了Flask实例化的核心对象 

定义了4个方法，`push`， `pop`， `__enter__`  , `__exit__`



设计思想，即有些对象的属性是在对象外部的，不属于对象本身的，所以可以设计上下文对象将对象外部的属性和操作关联起来,放到新的上下文对象中





### RequestContext

请求上下文，即封装请求对象Request

同样和AppContext一样，定义了4个方法，`push`， `pop`， `__enter__`  , `__exit__`









## 线程隔离

通过werkzeug 中的Local对象，实现线程隔离的数据操作







# 视图函数注册



## 装饰器方法

常用方法, 但是其实调用的是add_url_rule实现的

```
from flask import Flask

app = Flask(__name__)


@app.route('/hello/')
def hello():
    return 'Hello, Rick'

app.run(debug=True)
```



## 传递参数

通过`<>` 中包含的参数传递

```
@app.route('/book/search/<q>/<page>')
def search(q, page):
    pass
```





## add_url_rule

在使用基于类的视图，即插式图，需要使用该方法

但装饰器里面不需要输入view_func 

```
from flask import Flask

app = Flask(__name__)

def hello():
    return 'Hello, Rick'


app.add_url_rule('/hello/', view_func=hello)

app.run(debug=True)
```





# 导入配置文件

config.py

```
DEBUG=True
```



```
from flask import Flask

# from config import DEBUG #该方法可以省略了


app = Flask(__name__)
app.config.from_object('config')


@app.route('/hello')
def hello():
    return 'hello world'


app.run(debug=app.config['DEBUG'])

```



# response 对象

视图函数返回的是response 对象， make_response可以创建一个对象

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





# static

static文件分应用程序static和blueprint static



Flask 核心的对象调用，默认为app目录下面的static，可以手动指定目录, 如果是共享static文件，就放到app应用程序下面

```
app = Flask(__name__, static_folder='view_models/statics', static_url_path='')
```



Blueprint 方法

```
web = Blueprint('web', __name__, static_folder='', static_url_path='')
```





# template

在初始化Flask核心对象或者blueprint的时候, 传入参数

```
template_folder='templates'
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





# 插件



## wtforms

对传入的http参数进行验证





### 验证层

对参数校验， 一般防止在app下面，叫forms



