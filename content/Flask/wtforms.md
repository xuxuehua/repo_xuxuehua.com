---
title: "wtforms"
date: 2019-08-23 07:58
---
[TOC]



# WTForms

对表单的定义，验证(服务器端)，处理等操作





## 字段属性

字段属性的名称会作为对应HTML input元素的name 属性以及ID属性值



WTForms会在表单提交后根据表单类中字段的类型对数据进行处理，转换成对应的Python类型

WTForms 输出的字段HTML代码只会包含id和name属性，属性值均为表单类中对应的字段属性名称



### 添加表单属性

* render_kw 方法

```
username = StringField('Username', render_kw={'placeholder': 'Your Username'})
```

通过render_kw 设置了placeholder HTMl属性

```
<input type="text" id="username" name="username" placeholder="Your Username">
```



调用字段时传入

class由于是保留字段，使用class_替代

```
form.username(style='width: 200px;', class_='bar')
```

通过添加括号使用关键字参数的形式传入字段

```
u'<input class="bar" id="username" name="username" style="width: 200px; " type="text">'
```





BooleanField

复选框，值会被处理为True 或者False

```
<input type="checkbox">
```



DataField

文本字段，值会被处理为datetime.date对象

```
<input type="text">
```



DateTimeField

文本字段，值会被处理为datetime.datetime对象

```
<input type="text">
```



FileField

文件上传字段

```
<input type="file">
```





FloatField

浮点数字段，值会被处理为浮点类型

```
<input type="text">
```



IntegerField

整数字段，值会被处理为整型

```
<input type="text">
```



RadioField

一组单选按钮

```
<input type="radio">
```





SelectField

下拉列表

```
<select><option></option></select>
```





SelectMultipleField

多选下拉列表

```
<select multiple><option></option></select>
```





SubmitField

提交按钮

```
<input type="submit">
```





StringField

文本字段

```
<input type="text">
```





HiddenField

隐藏文本字段

```
<input type="hidden">
```





PasswordField

密码文本字段

```
<input type="password">
```





TextAreaField

多行文本字段

```
<textarea></textarea>
```





# 实例化字段类



## label

字段标签<label>的值，也就是渲染后显示在输入字段前的文字



## render_kw

一个字典，用来设置对应的HTML <input> 标签属性，比如传入`{'placeholder': 'Your name'}` ， 渲染后的HTML代码会将<input> 标签的placeholder属性设置为Your name



## validators 验证器

一个列表，包含一系列验证器，会在表单提交后被逐一调用验证表单数据

message 参数用来传入自定义错误消息，乳沟没有设置则使用内置的英文错误消息

![img](https://snipboard.io/KXSlk5.jpg)



```
name = StringField('Your Name', validators=[DataRequired(message=u'名字不能空')])
```



### 文件上传

input 标签中的type 方法设置为file，会在浏览器中渲染成一个文件上传字段

![img](https://snipboard.io/OrdwBJ.jpg)



HTML5 中的accept属性也可以实现类型过滤

```
<input type="file" id="profile_pic" name="profile_pic" accept=".jpg, .jpeg, .png, .gif">
```





#### 多文件上传

在input 标签中添加multiple 属性可以开启多选

创建表单时，直接使用Multiple File Field实现

```
from wtforms import MultipleFileField

class MultiUploadForm(FlaskForm):
		photo = MultipleFileField('Upload Image', validators={DataRequired()})
```





#### secure_filename

secure_filename 函数会对文件名进行过滤，传递文件名作为参数，会过滤掉所有危险的字符

```
from werkzeug import secure_filename
secure_filename('avatar! @#//#\\%$^&.jpg')
```



默认会过滤掉文件名中非ASCII字符，如果由非ASCII字符组成，会得到空文件名，为避免通常是使用统一的处理方式对上传的文件重新命名

```
def random_filename(filename):
		ext = os.path.splitext(filename)[1]
		new_filename = uuid.uuid4().hex + ext
		return new_filename
```









## default

字符串或者可调用对象，用来为表单字段设置默认值





## 表单提交

form标签声明中类型为submit的提交字段被单击时，会创建一个提交表单的HTTP请求，请求中包含表单各个字段的数据

![img](https://snipboard.io/ni4w5d.jpg)

> 使用POST方法提交表单，按照默认的编码类型，表单数据会被存储在请求主体
>
> ```
> POST /basic HTTP/1.0
> ...
> Content-Type: application/x-www-form-urlencoded
> Content-Length: 30
> ```



避免页面刷新和重载发送请求， 尽量不要让提交表单的POST请求作为最后一个请求，一般是在处理表单后返回一个重定向响应，这样会让浏览器重新发送一个新的GET请求到重定向的目标URL，最终，最后一个请求就变成了GET请求。



### 多表单提交

为区分表单， 需要设置不同的提交字段名称

```
class SigninForm(FlaskForm):
		username = StringField('Username', validators=[DataRequired(), Length(1, 20)])
		password = PasswordField("Password", validators=[DataRequired(), Length(8, 128)])
		submit1 = SubmitField('Sign in')
```

```
class RegisterForm(FlaskForm):
		username = StringField('Username', validators=[DataRequired(), Length(1, 20)])
		email = StringField('Email', validators=[DataRequired(), Email(), Length(1, 254)])
		password = PasswordField("Password", validators=[DataRequired(), Length(8, 128)])
		submit2 = SubmitField('Register')
```

```
@app.route('/multi-form', methods=['GET', 'POST'])
def multi_form():
		signin_form = SigninForm()
		register_form = RegisterForm()
		if signin_form.submit1.data and signin_form.validate():
				username = signin_form.username.data
				flash('%s', you just submit the Signin From. % username)
				return redirect(url_for('index'))
		if register_form.submit2.data and register_form.validate():
				username = register_form.username.data
				flash('%s, you just submit the Register Form.' % username)
				return redirect(url_for('index'))
		return render_template('2form.html', signin_form=sigin_form, register_form=register_form)
```

然后表单实例通过不同的变量名传入到模板中

```
...
<form method="post">
		{{ signin_form.csrf_token }}
		{{ form_field(sigin_form.username) }}
		{{ form_field(sigin_form.password) }}
		{{ sigin_form.submit1 }}
</form>
<h2>Register Form</h2>
<form method="post">
		{{ register_form.csrf_token }}
		{{ form_field(register_form.username) }}
		{{ form_field(register_form.email) }}
		{{ form_field(register_form.password) }}
		{{ register_form.submit2 }}
</form>
```







## 表单验证

### 客户端验证

使用HTML5 内置验证属性

type, required, min, max, accept

```
<input type="text" name="username" required>
```

可以在定义表单的时候通过render_kw 传入，或是在表单渲染的时候传入

```
{{ form.username(required='') }}
```



也可以使用JavaScript实现表单验证





### 服务器端验证

调用validate()方法，对字段逐个验证，若错误会存储到errors属性对应的字段中

```
class LoginForm(Form):
		username = StringField('Username', validators=[DataRequired()])
		password = PasswordField('Password', validators=[DataRequired(), Length(8, 128)])
		

form = LoginForm(username='', password='123')
form.validate()
form.errors
>>>
false
{'username': [u'This field is required.'], 'password': [u'Field must be at least 6 characters long.']}
```





## 表单方法

通过调用request.method 获取

```
if request.method == 'POST' and form.validate():
    pass
```

使用POST方法提交的表单，其数据会被Flask解析为一个字典，可以通过请求对象的form属性获取(request.form)

使用GET方法提交的表单的数据同样会被解析为字典，不过要通过请求对象的args属性获取(request.args)







# 自定义验证器



## 行内验证器

```
from wtforms import IntegerField, SubmitField
from wtforms.validators import ValidationError

class FortyTwoForm(FlaskForm):
    answer = IntegerField('The Number')
		submit = SubmitField()
		def validate_answer(form, field):
			  if field.data != 42:
			  		raise ValidationError('Must be 42.')
```

> 这里的validate_answer 用来验证对应字段



## 全局验证器

定义一个可以通用的验证器

```
from wtforms.validators import ValidationError
def is_42(form, field):
		if field.data != 42:
				raise ValidationError('Must be 42')


class FortyTwoForm(FlaskForm):
    answer = IntegerField('The Number', validators=[is_42])
    submit = SubmitField()
```



# Flask-WTF

集成了WTForms，可以在Flask中更方便的使用WTForms，附加了reCAPTHCA 支持

```
pipenv install flask-wtf
```



Flask-WTF定义表单时，仍然使用WTForms提供的字段类和验证器，创建方式是完全相同的，只不过表单类要集成Flask-WTF提供的Flask Form类, Flask Form 类继承自Form类，进行一些设置，并附加了类，并附加了一些辅助方法，以便与Flask集成

```
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length

class LoginForm(FlaskForm):
		username = StringField('Username', validators=[DataRequired()])
		password = PasswordField('Password', validators=[DataRequired(), Length(8, 128)])
		remember = BooleanField('Remember me')
		submit = SubmitField('Login')
```

> 配置键WTF_CSRF_ENABLED用来设置是否开启CSRF保护，默认为True，Flask-WTF会自动在实例化表单类时添加一个包含CSRF令牌值的隐藏字段，字段名为csrf_token







## CSRF

Flask-WTF 默认为每个表单启用了CSRF保护， 自动生成和验证CSRF令牌，需要程序密钥来进行签名

```
app.secret_key = 'secret string'
```













