---
title: "session"
date: 2020-01-20 16:45
---
[toc]



# Session

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



## 注意点

尽管session对象对Cookie进行签名并加密，这种方式仅能够保证session的内容不会被篡改，加密后的数据借助工具仍然可以被轻易读取，所以不能在session中存储敏感信息





## 原理

flask内置session本质上依靠上下文，当请求到来时，调用session_interface中的open_session方法解密获取session的字典，并保存在RequestContext.session中，也就是上下文中，然后在视图函数执行完毕后调用session_interface的save_session方法，将session以加密的方式写入response的cookie中，浏览器再保存数据。而第三方的session组件原理就是基于是open_session方法和save方法，从而实现session更多的session保存方案。





# flask-session

flask-session支持多种数据库session保存方案如：redis、memchached、mongodb甚至文件系统等。

```
pip3 install flask-session
```

## redis

```
import redis
from flask import Flask, session
from flask_session import Session
from datetime import timedelta

app = Flask(__name__)
app.debug = True
app.secret_key = 'adavafa'



app.config['SESSION_TYPE'] = 'redis' # session类型为redis
app.config['SESSION_PERMANENT'] = True # 如果设置为True，则关闭浏览器session就失效。
app.config['PERMANENT_SESSION_LIFETIME']=timedelta(seconds=20)
#一个持久化的会话的生存时间，是一个datetime.timedelta对象，也可以用一个整数来表示秒,前提设置了PERMANENT_SESSION_LIFETIME为True
app.config['SESSION_USE_SIGNER'] = False # 是否对发送到浏览器上session的cookie值进行加密,默认False 
app.config['SESSION_KEY_PREFIX'] = 'flask-session' # 保存到redis中的key的前缀
app.config['SESSION_COOKIE_NAME']= 'session_id'  # 保存在浏览器的cookie名称
app.config['SESSION_REDIS'] = redis.Redis(host='10.1.210.33', port=‘6379',password=‘123123') # 用于连接redis的配置


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
```



## Memchached

```
import memcache
from flask import Flask, session
from flask_session import Session
from datetime import timedelta

app = Flask(__name__)
app.debug = True
app.secret_key = 'adavafa'



app.config['SESSION_TYPE'] = ‘memcached' # session类型为memcached 
app.config['SESSION_PERMANENT'] = True # 如果设置为True，则关闭浏览器session就失效。
app.config['PERMANENT_SESSION_LIFETIME']=timedelta(seconds=20)
#一个持久化的会话的生存时间，是一个datetime.timedelta对象，也可以用一个整数来表示秒,前提设置了PERMANENT_SESSION_LIFETIME为True
app.config['SESSION_USE_SIGNER'] = False # 是否对发送到浏览器上session的cookie值进行加密,默认False
app.config['SESSION_KEY_PREFIX'] = 'flask-session' # 保存到缓存中的key的前缀
app.config['SESSION_COOKIE_NAME']= 'session_id'  # 保存在浏览器的cookie名称
app.config['SESSION_MEMCACHED'] = memcache.Client(['10.1.210.33:12000']) #连接


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
```



## Filesystem

```
from flask import Flask, session
from flask_session import Session
from datetime import timedelta

app = Flask(__name__)
app.debug = True
app.secret_key = 'adavafa'



app.config['SESSION_TYPE'] = 'filesystem' # session类型为filesystem
app.config['SESSION_FILE_DIR']='/opt/db' #文件保存目录
app.config['SESSION_FILE_THRESHOLD'] = 300 # 存储session的个数如果大于这个值时，开始删除

app.config['SESSION_PERMANENT'] = True # 如果设置为True，则关闭浏览器session就失效。
app.config['PERMANENT_SESSION_LIFETIME']=timedelta(seconds=20)
#一个持久化的会话的生存时间，是一个datetime.timedelta对象，也可以用一个整数来表示秒,前提设置了PERMANENT_SESSION_LIFETIME为True
app.config['SESSION_USE_SIGNER'] = False # 是否对发送到浏览器上session的cookie值进行加密,默认False
app.config['SESSION_KEY_PREFIX'] = 'flask-session' # 保存到文件中的key的前缀
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
```



## mongodb

```
import pymongo
from flask import Flask, session
from flask_session import Session
from datetime import timedelta

app = Flask(__name__)
app.debug = True
app.secret_key = 'adavafa'


app.config['SESSION_TYPE'] = 'mongodb' # session类型为mongodb
app.config['SESSION_MONGODB'] = pymongo.MongoClient('localhost',27017)
app.config['SESSION_MONGODB_DB'] = '数据库名称'
app.config['SESSION_MONGODB_COLLECT'] = '表名称'


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
```



## sqlalchemy

有bug，在断开session会导致字段为空

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