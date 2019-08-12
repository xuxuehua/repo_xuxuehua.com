---
title: "view_function"
date: 2019-08-10 11:51
---
[TOC]



# 视图函数

负责管理URL和函数之间的映射



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



### 多URL绑定

两个URL均会触发say_hello函数

```
@app.route('/hi')
@app.route('/hello')
def say_hello():
    return '<h1>Hello World</h1>'
```



### 动态URL

```
@app.route('/greet/<name>')
def greet(name):
    return '<h1>Hello, %s!</h1>' % name
```



设定默认值

```
@app.route('/greet')
@app.route('/greet/<name>')
def greet(name='Programmer'):
    return '<h1>Hello, %s! </h1>' % name
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



## url_for / endpoint

动态修改URL规则

默认第一个参数为endpoint，endpoint用来标记一个视图函数以及所对应的URL规则

endpoint的默认值为视图函数的名称



```
@app.route('/')
def index():
    return '<h1>Hello World</h1>'
```

> url_for('index') 即可获取到对应的URL `/`



```
@app.route('/greet/<name>')
def greet(name='Programmer'):
    return '<h1>Hello, %s! </h1>' % name
```

> url_for('greet', name='Rick') 获取为`/greet/Rick`



## 转换器

转换器通过特定的规则制定

```
<转换器：变量名>
```



### int

转换为整数

```
@app.route('goback/<int:year>'')
def go_back(year):
    return '<p>Welcome to %d </p>' % ()
```

>  对year变量转换为整数



### any

匹配一系列给定值的一个元素

```
<any(value1, value2, ...): 变量名>
```



```
@app.route('/colors/<any(blue, white, red):color>')
def three_colors(color):
    return '<p>three colors</p>'
```

> 访问http://127.0.0.1:5000/colors/<color> 时，color替换为any转换器中的其他值，均会404错误响应



可以传入预先定的一个列表

```
colors = ['blue', 'white', 'red']
@app.route('/colors/<any(%s):color>' % str(colors)[1:-1])
....
```





### path

包含斜线的字符串

static路由的URL规则中的filename变量就使用了这个转换器



## 请求钩子

对请求进行预处理preprocessing和后处理postprocessing操作,在请求处理的不同阶段执行的处理函数

![img](https://snag.gy/4VQg59.jpg)



### before_first_request

运行程序前的初始化操作，比如创建数据库表，添加管理员用户



### before_request

如记录用户最后在线时间，通过用户发送的请求时间来实现，避免在每个视图函数都添加更新在线时间的代码，可以在此调用该袋米



### after_request

对数据库进行更新，插入等，之后需要将更改提交到数据库中，可以在此调用



### teardown_request

在请求之后，关闭数据库连接，通过在使用请求钩子注册的函数中添加代码实现



### after_this_request

必须接收一个响应类函数作为参数，并且放回同一个或者更新后的响应对象





# request 对象

封装了客户端发来的所有请求数据

## args

通过字典方式取值, 推荐使用get方法

```
name = request.args.get('name', 'Flask')
```



若使用字典方式取值

```
name = request.args['name']
```

> 没有对应的健，会报出http 404 错误， bad request



若无对映值，放回None



## get_json

# response 对象



## make_response 

视图函数返回的是response 对象， make_response方法将试图函数返回值转换为响应对象

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



### MIME 类型

content-type 可以定义MIME类型， 

HTML为text/html

png 为 image/png

纯文本为text/plain

xml为 application/xml

json为 application/json



#### json 

```
from flask import Flask, make_response, json

@app.route('/foo')
def foo():
    data = {
        'name': 'Rick',
        'gender': 'male'
    }
    response = make_response(json.dumps(data))
    response.mimetype = 'application/json'
    return response
```



#### jsonify （替代json）

一般不直接使用json模块的dumps和loads方法，jsonify函数只需对传入的参数进行序列化即可

```
from flask import jsonify

@app.route('/foo')
def foo():
    return jsonify({name: 'Rick', gender: 'male'})
```



jsonify 函数默认生产200 响应，可以附加自定义响应类型

```
@app.route('/foo')
def foo():
    return jsonify(message="Error! "), 500
```







## redirect 重定向

redirect函数生成重定向响应

```
from flask import Flask, redirect

@app.route('/hello')
def hello():
    return redirect('http://www.xuxuehua.com')
```



### url_for

重定向到其他视图函数

```
from flask import Flask, redirect, url_for

@app.route('/hi')
def hi():
    return redirect(url_for('hello'))

@app.route('/hello')
def hello():
    pass
```



## abort

该函数不需要使用return语句，一旦被调用，后面的代码就不会被执行

```
from flask import Flask, abort

@app.route('/404')
def not_found():
    abort(404)
```





## set_cookie()

用来设置一个cookie

```
from flask import Flask, make_response

@app.route('/set/<name>')
def set_cookie(name):
    response = make_response(redirect(url_for('hello')))
    response.set_cookie('name', name)
    return response
```

> 生成的Set-Cookie字段为 "Set-Cookie: name=Grey;Path=/"



### secret_key

把密钥写进系统环境变量中

```
SECRET_KEY=secret string

###

import os 
app.secret_key = os.getenv('SECRET_KEY', 'secret string')
```

> 这里的secret string 需要时一段特殊的随机密钥值



## session

session可以保存cookie信息

```
from flask import redirect, session, url_for

@app.route('/login')
def login():
    session['logged_in'] = True
    return redirect(url_for('hello'))
```

session 中的数据可以像字典一样通过键读取，或时使用get方法



















