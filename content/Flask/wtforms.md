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



### BooleanField

复选框，值会被处理为True 或者False

```
<input type="checkbox">
```



### DataField

文本字段，值会被处理为datetime.date对象

```
<input type="text">
```



### DateTimeField

文本字段，值会被处理为datetime.datetime对象

```
<input type="text">
```



### FileField

文件上传字段

```
<input type="file">
```





### FloatField

浮点数字段，值会被处理为浮点类型

```
<input type="text">
```



### IntegerField

整数字段，值会被处理为整型

```
<input type="text">
```



### RadioField

一组单选按钮

```
<input type="radio">
```





### SelectField

下拉列表

```
<select><option></option></select>
```





### SelectMultipleField

多选下拉列表

```
<select multiple><option></option></select>
```





### SubmitField

提交按钮

```
<input type="submit">
```





### StringField

文本字段

```
<input type="text">
```





### HiddenField

隐藏文本字段

```
<input type="hidden">
```





### PasswordField

密码文本字段

```
<input type="password">
```





### TextAreaField

多行文本字段

```
<textarea></textarea>
```





## 实例化字段类



### label

字段标签<label>的值，也就是渲染后显示在输入字段前的文字



### render_kw

一个字典，用来设置对应的HTML <input> 标签属性，比如传入`{'placeholder': 'Your name'}` ， 渲染后的HTML代码会将<input> 标签的placeholder属性设置为Your name



### validators

一个列表，包含一系列验证器，会在表单提交后被逐一调用验证表单数据

message 参数用来传入自定义错误消息，乳沟没有设置则使用内置的英文错误消息

![img](https://snipboard.io/KXSlk5.jpg)



```
name = StringField('Your Name', validators=[DataRequired(message=u'名字不能空')])
```







### default

字符串或者可调用对象，用来为表单字段设置默认值







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













