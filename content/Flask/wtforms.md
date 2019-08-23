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





# Flask-WTF

集成了WTForms，可以在Flask中更方便的使用WTForms，附加了reCAPTHCA 支持

```
pipenv install flask-wtf
```



## CSRF

Flask-WTF 默认为每个表单启用了CSRF保护， 自动生成和验证CSRF令牌，需要程序密钥来进行签名

```
app.secret_key = 'secret string'
```











