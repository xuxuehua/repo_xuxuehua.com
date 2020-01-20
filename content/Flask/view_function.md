---
title: "view_function"
date: 2019-08-10 11:51
---
[TOC]



# 视图函数

负责管理URL和函数之间的映射

即MVC中的Controller



## 装饰器方法

常用方法, 但是其实调用的是add_url_rule实现的

```
from flask import Flask

app = Flask(__name__)


@app.route('/hello')  # 定义路由， 不加斜杠即不兼容用户输入唯一URL的情况
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



## URL 传递参数

通过`<>` 中包含的参数传递

```
@app.route('/book/search/<q>/<page>')
def search(q, page):
    pass
```



### HTTP referer

获取上一个页面URL， 请求数据包含的HTTP_REFERER字段记录了用户所在原站点URL， 在Flask中， 通过request.referrer获取

```
return redirect(request.referrer or url_for('hello'))
```

> referrer字段可能会是空值



### 查询参数 next

通过在URL中手动加入包含当前页面URL的查询参数， 这个查询参数一般命名为next

```
from flask import request

@app.route('/foo')
def foo():
    return '<h1>Foo Page</h1><a href="%s"> % url_for('do_something', next=request.full_page)'
```

> request.full_page 可以获取当前页面的完整路径



### is_safe_url() 验证URL

不验证next变量指向的URL，是否处于本地应用内，程序很容易被重定向到外部地址, 比如虚假完整的镜像网站

```
http://127.0.0.1:5000/do-something?next=http://xurick.com
```



使用验证函数确保安全性

```
from urlparse import urlparse, urljoin
from flask import request

def is_safe_url(target):
    ref_url = urlparse(request.host_url)
    test_url = urlparse(urljoin(request.host_url, target))
    return test_url.scheme in ('http', 'https') and \
    ref_url.netloc == test_url.netloc
```

```
def redirect_back(default='hello', **kwargs):
    for target in request.args.get('next'), request.referrer:
        if not target:
            continue
        if is_safe_url(target):
            return redirect(target)
    return redirect(url_for(default, **kwargs))
```



### 返回空值

```
@app.route('/post/delete/<int:post_id>', methods=['DELETE'])
def delete_post(post_id):
    ...
    return '', 204
```



## add_url_rule

在使用基于类的视图，即插式图，需要使用该方法， 实现了route装饰器的效果

```
add_url_rule(rule, endpoint, view_function)
```

但装饰器里面不需要输入view_func ， 使用起来更方便

```
from flask import Flask

app = Flask(__name__)

def hello():
    return 'Hello, Rick'


app.add_url_rule('/hello/', view_func=hello)

app.run(debug=True)
```





查看当前程序注册的所有路由

```
$ flask routes
```



## url_for / endpoint

动态修改URL规则

默认第一个参数为endpoint，endpoint用来标记一个视图函数以及所对应的URL规则

endpoint的默认值为视图函数的名称

依赖上下文环境

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

转换器通过特定的规则制定，是对url地址的限制

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





### 自定义

```
from flask import Flask
from werkzeug.routing import BaseConverter  # 导入
 
app = Flask(__name__)
 
 
# 1. 自定义转换器 (继承BaseConverter)
class MobileConverter(BaseConverter):
    # 第一个参数url_map是固定的。(url_map一般直接交给父类BaseConverter使用)
    def __init__(self, url_map):
        # 先调用父类BaseConverter的初始化方法
        super(MobileConverter, self).__init__(url_map)
        # flask会自动通过regex属性来进行路由的正则匹配
        self.regex = r'1[34578]\d{9}'  # 匹配手机号的正则
 
 
# 2. 将自定义的转换器添加到flask的应用中
app.url_map.converters["mobile"] = MobileConverter
 
 
# 127.0.0.1:5000/send/18612345678
@app.route("/send/<mobile:mobile_num>")  # mobile 使用自定义的转换器
def send_sms(mobile_num):
    return "send sms to %s" % mobile_num
 
 
if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000, debug=True)
```



#### 带参数

```
from flask import Flask
from werkzeug.routing import BaseConverter  # 导入
 
app = Flask(__name__)
 
 
# 1. 自定义转换器 (继承BaseConverter)
class RegexConverter(BaseConverter):
    # 第一个参数url_map是固定的。(url_map一般直接交给父类BaseConverter使用)
    # 接收参数regex
    def __init__(self, url_map, regex):
        super(RegexConverter, self).__init__(url_map)
        # 将路由中正则表达式参数传递给regex属性
        # flask会自动通过regex属性来进行路由的正则匹配
        self.regex = regex
 
 
# 2. 将自定义的转换器添加到flask的应用中
app.url_map.converters["re"] = RegexConverter
 
 
# 127.0.0.1:5000/send/18612345678
@app.route("/send/<re(r'1[34578]\d{9}'):mobile_num>")  # ()表示传递给转换器的参数(正则表达式)
def send_sms(mobile_num):
    return "send sms to %s" % mobile_num
 
 
if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000, debug=True)
```



#### to_python / to_url

```
from flask import Flask, redirect, url_for
from werkzeug.routing import BaseConverter  # 导入
 
app = Flask(__name__)
 
 
# 1. 自定义转换器 (继承BaseConverter)
class RegexConverter(BaseConverter):
 
    def __init__(self, url_map, regex):
        super(RegexConverter, self).__init__(url_map)
        self.regex = regex
 
    # 重写父类的to_python()方法。 (可以对url参数做预处理，类型转换等)
    def to_python(self, value):
        # value参数就是url中匹配出的内容。
        # 可以在to_python()方法中对url参数做预处理、类型转换等 (转换器本质)
        return value  # return的返回值就是传递给视图函数的值
 
    # 重写父类的to_url()方法。 使用url_for方法的时候被调用(url反向解析)
    def to_url(self, value):
        # value就是url_for()中传递的参数。
        # return "15811111111"
        return value   # 返回值就是url路由正则匹配出的url部分。
 
 
# 2. 将自定义的转换器添加到flask的应用中
app.url_map.converters["re"] = RegexConverter
 
 
# 127.0.0.1:5000/send/18612345678
@app.route("/send/<re(r'1[34578]\d{9}'):mobile_num>")
def send_sms(mobile_num):
    return "send sms to %s" % mobile_num
 
 
@app.route("/index")
def index():
    # url反向解析，根据send_sms视图获取对应的url。 (会调用转换器的to_url()方法)
    url = url_for("send_sms", mobile_num="18912341235")  # mobile_num对应视图函数接收的形参。
    # url = "/send/18912341235"
    return redirect(url)
 
 
if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000, debug=True)
```





## 请求钩子

对请求进行预处理preprocessing和后处理postprocessing操作,在请求处理的不同阶段执行的处理函数

![image-20200113085508339](image-20200113085508339.png)



### before_first_request

在第一次请求之前可以执行的函数，该函数只会执行一次

运行程序前的初始化操作，比如创建数据库表，添加管理员用户



### before_request

每一次请求之前可以执行某个特定功能的函数

一般可以用来检查用户请求是否合法，权限检查等场景

如记录用户最后在线时间，通过用户发送的请求时间来实现，避免在每个视图函数都添加更新在线时间的代码，可以在此调用该代码



### after_request

每一次请求之后可以执行某个特定功能的函数

对数据库进行更新，插入等，之后需要将更改提交到数据库中，可以在此调用

可以产生csrf_token的验证码场景

该钩子函数要求必须返回response 对象



### teardown_request

在请求之后，关闭数据库连接，通过在使用请求钩子注册的函数中添加代码实现

每次请求之后，都会调用，会接收一个参数，参数是服务器出现的错误信息



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

依赖上下文环境，因为其内部调用中使用了current_app变量

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

> 这里的hello是视图函数



## abort

该函数不需要使用return语句，一旦被调用，后面的代码就不会被执行

```
from flask import Flask, abort

@app.route('/404')
def not_found():
    abort(404)
```



实现用户登陆

```
from flask import session, abort


@app.route('/admin')
def admin():
    """
    使用字典方式存入key信息
    session['logged_in'] = True
    """
    if 'logged_in' not in session:
        abort(403)
    return 'Welcome to admin page.'
```





登出用户

```
from flask import session, redirect, url_for


@app.route('/logout')
def logout():
    if 'logged_in' in session:
        session.pop('logged_in')
    return redirect(url_for('login'))
```







## set_cookie()

Flask 操作cookie，是通过response 对象来操作，可以在response返回之前，通过response.set_cookie 来设置

```
from flask import Flask, make_response

@app.route('/set/<name>')
def set_cookie(name):
    response = make_response(redirect(url_for('hello')))
    response.set_cookie('name', name)
    return response
```

> 生成的Set-Cookie字段为 "Set-Cookie: name=Grey;Path=/"



其几个参数需要注意

```
key：设置的cookie的key。
value：key对应的value。
max_age：改cookie的过期时间，如果不设置，则浏览器关闭后就会自动过期。
expires：过期时间，应该是一个datetime类型。
domain：该cookie在哪个域名中有效。一般设置子域名，比如cms.example.com。
path：该cookie在哪个路径下有效。
```

使用：

```
获取：request.cookies.get(key, '默认值')
设置：resp.set_cookie(key, value, max_age=整数)
删除：resp.delete_cookie(key)
```





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

Flask 的 session 默认是client side 的，session是写入到浏览器的cookie中，当浏览器关闭，cookie会自动消失

将session数据加密，然后存储在cookie中，这种专业术语叫做client side session， flask采用这种方式

```
from flask import redirect, session, url_for

@app.route('/login')
def login():
    session['logged_in'] = True
    return redirect(url_for('hello'))
```

>  session 中的数据可以像字典一样通过键读取，或时使用get方法
>



sqlalchemy

```
import redis
from flask import Flask, session
from flask_session import Session 
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.debug = True
app.secret_key = 'adavafa'

# 设置数据库链接
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:dev@127.0.0.1:3306/devops?charset=utf8'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
# 实例化SQLAlchemy
db = SQLAlchemy(app)
app.config['SESSION_TYPE'] = 'sqlalchemy' # session类型为sqlalchemy
app.config['SESSION_SQLALCHEMY'] = db # SQLAlchemy对象
app.config['SESSION_SQLALCHEMY_TABLE'] = '表名' # session要保存的表名称

app.config['SESSION_PERMANENT'] = True # 如果设置为True，则关闭浏览器session就失效。
app.config['PERMANENT_SESSION_LIFETIME']=timedelta(seconds=20)
#一个持久化的会话的生存时间，是一个datetime.timedelta对象，也可以用一个整数来表示秒,前提设置了PERMANENT_SESSION_LIFETIME为True
app.config['SESSION_USE_SIGNER'] = False # 是否对发送到浏览器上session的cookie值进行加密,默认False
app.config['SESSION_KEY_PREFIX'] = 'flask-session' # 保存的session的key的前缀
app.config['SESSION_COOKIE_NAME']= 'session_id'  # 保存在浏览器的cookie名称


#其他配置，不经常使用
app.config['SESSION_COOKIE_DOMAIN']='127.0.0.1' # 设置cookie的域名，不建议设置默认为server_name
app.config['SESSION_COOKIE_PATH']='/' # 会话cookie的路径。 如果未设置，则cookie将对所有url有效，默认为'/'
app.config['SESSION_COOKIE_HTTPONLY']=True # 是否启动httponly,默认为true，为了防止xss脚本访问cookie




Session(app)


@app.route('/login')
def index():
 session["username"]="jack"
 return 'login'


if __name__ == '__main__':
 app.run()


###使用SQLAlchemy时候先确保数据库和表都存在
在命令行中创建表
#>>> from app import db
#>>> db.create_all()
```



 设置数据库

```
CREATE TABLE public.flask_session_table (
    id integer PRIMARY KEY NOT NULL,
    session_id character varying(255),
    data bytea,
    expiry timestamp without time zone
);
```



### 注意点

尽管session对象对Cookie进行签名并加密，这种方式仅能够保证session的内容不会被篡改，加密后的数据借助工具仍然可以被轻易读取，所以不能在session中存储敏感信息





















