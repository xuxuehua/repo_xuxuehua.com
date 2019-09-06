---
title: "email"
date: 2019-08-29 14:31
---
[TOC]



# Flask-Mail

扩展第三方邮件服务, 通过SMTP服务来发送邮件



```
pip install flask-mail
```



```
from flask_mail import Mail
app = Flask(__name__)
...
mail = Mail(app)
```



## 配置变量

![img](https://snipboard.io/IMGLhj.jpg)



STARTTLS 是另一种加密方式，会对不安全的连接进行升级(使用SSL或者TLS)， 

```
MAIL_USE_TLS = True
MAIL_PORT = 587
```



SSL/TLS加密

```
MAIL_USE_SSL = True
MAIL_PORT = 465
```



## 常用邮件服务

![img](https://snipboard.io/vOXpV9.jpg)

163 SMTP不支持STARTTLS， 需要使用SSL/TLS 

对于需要发送大量事务性邮件，使用自己配置的SMTP或者Send Grid， Mailgun等



## 服务配置

```
import os 
from flask import Flask
from flask_mail import Mail 
app = Flask(__name__)
app.config.update(
		...
		MAIL_SERVER = os.getenv('MAIL_SERVER')
		MAIL_PORT = 587
		MAIL_USE_TLS = True
		MAIL_USERNAME = os.getenv('MAIL_USERNAME')
		MAIL_PASSWORD = os.getenv('MAIL_PASSWORD')
		MAIL_DEFAULT_SENDER = ('Rick Xu', os.getenv('MAIL_USERNAME'))
)

mail = Mail(app)
```



## 发信

```
from flask_mail import Mail, Message 
...
mail = Mail(app)
...
def send_mail(subject, to, body):
		message = Message(subject, recipients=[to], body=body)
		mail.send(message)
```



```
@app.route('/subscribe', methods=["GET", "POST"])
def subscribe():
		form = SubscribeForm()
		if form.validate_on_submit():
				email = form.email.data
				flash('Welcome you')
				send_email("Subscribe Success!", email, 'Hello, thanks for subscribing')
				return redirect(url_for('index'))
		return render_template('index.html', form=form)
```





