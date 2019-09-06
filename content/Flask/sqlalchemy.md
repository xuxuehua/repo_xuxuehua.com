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

扩展Flask-SQLAlchemy 集成了SQLAlchemy，简化了连接数据库服务器，管理数据库操作会话等工作

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





