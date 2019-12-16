---
title: "sqlalchemy"
date: 2019-08-28 08:58
---
[TOC]



# SQLAlchemy



## ORM

ORM 把底层的SQL数据实体转化成高层的Python对象

表 -> Python 类

字段（列） -> 类属性

记录（行） -> 类实例



## URI 

Uniform Resource Identifier 统一资源标识符，包含各种属性的字符串，用于连接数据库的信息

![img](https://snipboard.io/uLvzNk.jpg)





# Flask-SQLAlchemy

扩展Flask-SQLAlchemy 集成了SQLAlchemy，简化了连接数据库服务器，管理数据库操作会话等工作， 但是底层还是sqlalchemy，提供了更加人性的API

Flask-SQLAlchemy 使用事物会话，可通过db.session 属性获取

默认Flask-SQLAlchemy会自动为模型类生成一个`__repr__()` 方法，可以重新定义返回有用的信息	

```
class Note(db.Model):
		...
		def __repr__(self):
		    return '<Note %r> % self.body'
```



## 数据类型

![img](https://snipboard.io/aZ6JC2.jpg)



### 字段参数

![img](https://snipboard.io/3CDmYM.jpg)



## 查询方法

![img](https://snipboard.io/N38pxq.jpg)



```
Note.query.all()
```



### 查询过滤器

![img](https://snipboard.io/wsgA1h.jpg)





### 关系属性

![img](https://snipboard.io/eL2v3y.jpg)



### 关系记录加载方式

![img](https://snipboard.io/lGz6Mr.jpg)

dynamic 选项仅用于集合关系属性，不可用于多对一，一对一或是在关系函数中将uselist参数设置为False的情况，避免使用dynamic动态加载所有集合的关系属性对应记录，意味着每次操作关系都会执行一次SQL查询，会有潜在的性能问题。

大多数情况只需要使用默认的select，返回大量的记录，只有需要对关系属性返回的结果附加额外的查询时才需要使用动态加载(lazy='dynamic')









## 删除操作

为了防范CSRF攻击，删除修改数据的操作不能通过GET请求实现，正确的做法是为了删除操作创建一个表单

```
class DeleteNoteForm(FlaskForm):
		submit = SubmitField('Delete')
		

@app.route('/delete/<int:note_id>', method=['POST'])	#这里仅填入POST
def delete_node(note_id):
		form = DeleteForm()
		if form.validate_on_submit():
				note = Note.query.get(note_id)
				db.session.delete(note)
				db.session.commit()
				flash('Your note is deleted.')
		else:
				abort(400)
		return redirect(url_for('index')
```

```
{% for note in notes %}
<div class="note">
	<p>{{ note.body }}</p>
	<a class="btn" href="{{ url_for('edit_note', note_id=note.id) }}">Edit</a>
	<form method="post" action="{{ url_for('delete_note', note_id=note.id) }}">
		{{ form.csrf_token }}
		{{ form.submit(class="btn") }}
	</form>
</div>
{% endfor %}
```







# 上下文数据库操作

As a general rule, the application should manage the lifecycle of the session *externally* to functions that deal with specific data. This is a fundamental separation of concerns which keeps data-specific operations agnostic of the context in which they access and manipulate that data.

E.g. **don’t do this**:

```
### this is the **wrong way to do it** ###

class ThingOne(object):
    def go(self):
        session = Session()
        try:
            session.query(FooBar).update({"x": 5})
            session.commit()
        except:
            session.rollback()
            raise

class ThingTwo(object):
    def go(self):
        session = Session()
        try:
            session.query(Widget).update({"q": 18})
            session.commit()
        except:
            session.rollback()
            raise

def run_my_program():
    ThingOne().go()
    ThingTwo().go()
```

Keep the lifecycle of the session (and usually the transaction) **separate and external**:

```
### this is a **better** (but not the only) way to do it ###

class ThingOne(object):
    def go(self, session):
        session.query(FooBar).update({"x": 5})

class ThingTwo(object):
    def go(self, session):
        session.query(Widget).update({"q": 18})

def run_my_program():
    session = Session()
    try:
        ThingOne().go(session)
        ThingTwo().go(session)

        session.commit()
    except:
        session.rollback()
        raise
    finally:
        session.close()
```

The most comprehensive approach, recommended for more substantial applications, will try to keep the details of session, transaction and exception management as far as possible from the details of the program doing its work. For example, we can further separate concerns using a [context manager](http://docs.python.org/3/library/contextlib.html#contextlib.contextmanager):

```
### another way (but again *not the only way*) to do it ###

from contextlib import contextmanager

@contextmanager
def session_scope():
    """Provide a transactional scope around a series of operations."""
    session = Session()
    try:
        yield session
        session.commit()
    except:
        session.rollback()
        raise
    finally:
        session.close()


class ThingOne(object):
    def go(self, session):
        session.query(FooBar).update({"x": 5})

class ThingTwo(object):
    def go(self, session):
        session.query(Widget).update({"q": 18})
 
 
def run_my_program():
    with session_scope() as session:
        ThingOne().go(session)
        ThingTwo().go(session)
```

