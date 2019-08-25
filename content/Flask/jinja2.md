---
title: "jinja2"
date: 2019-06-02 16:21
---


[TOC]



# jinja2

使用.来获取变量属性

如user.username 等同于获取字典中user['username']



1. 模板不支持多继承，也就是子模板中定义的块，不可能同时被两个父模板替换。
2. 模板中不能定义多个同名的块，子模板和父模板都不行，因为这样无法知道要替换哪一个部分的内容。

建议在`endblock`关键字后也加上块名，比如`{% endblock block_name %}`。虽然对程序没什么作用，但是当有多个块嵌套时，可读性好很多。



## 模版继承

super函数可以向基类模板中的块追加内容, 即父类块中添加内容



将父模板”layout.html”改为

```
<!doctype html>
<head>
    {% block head %}
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    <title>{% block title %}{% endblock %}</title>
    {% endblock %}
</head>
<body>
    <div class="page">
    {% block body %}
    {% endblock %}
    </div>
</body>
```

并在子模板里，加上”head”块和”title”块

```
{% block title %}Block Sample{% endblock %}
{% block head %}
    {{ super() }}
    <style type="text/css">
        h1 { color: #336699; }
    </style>
{% endblock %}
```

父模板同子模板的”head”块中都有内容。运行后，你可以看到，父模板中的”head”块语句先被加载，而后是子模板中的”head”块语句。这就得益于我们在子模板的”head”块中加上了表达式`{{ super() }}`。



### 子模板

避免块混乱，结束标签可以指明块名称

```
{% block body %}
	...
{% endblock body %}
```



### 局部模版 import插入

使用include标签插入，一般为了和普通模板区分开，通常以下划线开头

```
{% include '_banner.html' %}
```



默认情况下包含include一个局部模板会传递当前上下文到局部模板中，但是import不会(引入macro).

具体来说，使用render_template() 函数渲染一个foo.html模板时，这个foo.html 的模板上下文中包含下列对象

```
Flask 使用内置的模板上下文处理函数提供的g, session, config, request
扩展使用内置的模板上下文处理函数提供的变量
自定义模板上下文处理器传入的变量
使用render_tempalte() 函数传入的变量
Jinja2 和Flask内置以及自定义的全局对象
Jinja2 内置及自定义过滤器
Jinja2 内置及自定义测试器
```



然而导入一个并非直接渲染的模板(macros.html， 这个模板仅仅包含下列对象

```
Jinja2 和Flask内置以及自定义的全局对象
Jinja2 内置及自定义过滤器
Jinja2 内置及自定义测试器
```



若需显示使用with context 声明

```
{% from "macros.html" import foo with context %}
```





## 内置全局函数

| 函数                                    | 说明                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| Range([start, ] stop[, step])           | 和Python中的range()用法相同                                  |
| lipsum(n=5, html=True, min=20, max=100) | 生成随机文本(lorem ipsum) ，可以在测试时用来填充页面，默认生成5段HTML文本，每段包含20-100个单词 |
| dict(**items)                           | 和Python中的dict()用法相同                                   |



### Flask 内置全局函数

| 函数                   | 说明                    |
| ---------------------- | ----------------------- |
| url_for()              | 用于生成URL的函数       |
| get_flashed_messages() | 用于获取flash消息的函数 |



#### url_for 函数

通过相对固定的endpoint来构建URL地址， 即生成URL地址

```
 <link rel="stylesheet" href="{{ url_for('static', filename='test.css') }}">
```

> static 是endpoint地址



### 自定义全局函数 template_global

使用template_global 装饰器直接将函数注册为模板函数

```
@app.template_global()
def bar():
    return 'I am bar'
```



## 模板环境变量

删除所在行之前的空格和制表符

```
app.jinja_env.trim_blocks = True
app.jinja_env.lstrip_blocks = True
```



# flash 消息闪现

flash函数发送的消息存储在session中，需要使用全局函数get_flashed_messages() 获取消息



Flask， Jinja2和Werkzeug等相关依赖均将文本类型设定为Unicode

需要在HTMLheader标签中添加编码声明

```
<meta charset="utf-8">
```



# 宏macro

把一部分模板代码封装到宏里面，使用传递的参数来构建内容，最后返回构建后的内容

通常把宏存储在单独的文件中，如macros.html 或者_macros.html



```
{% macro qux(amount=1) %}
	{% if amount == 1 %}
		I am qux
	{% elif amount > 1 %}
		We are quxs.
	{% endif %}
{% endmacro %}
```



使用import 语句导入，然后作为函数调用，传入必要的参数

```
{% from 'macros.html' import qux %}
	.
{{ qux(amount=5) }}
```



## 加载静态资源

```
{% macro static_file(type, filename_or_url, local=True) %}
	{% if local %}
		{% set filename_or_url = url_for('static', filename=filename_or_url) %}
	{% endif %}
	{% if type == 'css' %}
		<link rel="stylesheet", href="{{ filename_or_url }}" type="text/css">
	{% elif type == 'js' %}
		<script type="text/javascript" src="{{ filename_or_url }}"></script>
	{% endif %}
	{% elif type == 'icon' %}
		<link rel="icon" href="{{ filename_or_url }}">
	{% endif %}
{% endmacro %}
```





# filter  过滤器

用来修改和过滤变量值的特殊函数

```
{{ name|title }}
```

> name变量使用title 过滤器



```
{% filter upper %}
    This text becomes upper case.
{% endfilter %}
```







## default

内置过滤器，类似于管道命令，设置默认值并作为参数传入

```
{% extends 'layout.html' %}
{% block content %}
    {{ super() }}
    {{ data.age }}
    {% if data.age < 18 %}
        {{ data.name }}
    {% elif data.age == 30 %}
        {{ data.school | default('Unnamed') }}
    {% else %}
        {{ data.age }}
    {% endif %}
{% endblock %}
```

> 默认data里面没有school这个键，所以会显示default所定义的Unnamed信息





## length

内置过滤器，返回变量长度

```
{{ data | length() }}
```

> 获取到data的长度信息





## safe

将变量值标记为安全，避免转义

不要直接对用户输入的内容使用safe过滤器，否则很容器被植入恶意代码，导致XSS攻击



## 自定义过滤器 template_filter()

使用app.template_filter() 装饰器可以自定义过滤器

```
from flask import Markup

@app.template_filter()
def musical(s):
    return s + Markup(' &#9835; ')
```

> musical过滤器会在被过滤的变量字符后面添加一个音符图标'

# 测试器

测试变量或者表达式



## number 

内置测试器，判断变量是否为数字

```
{% if age is number %}
	{{ age * 365 }}
{% else %}
	Invalid number
{% endif %}
```



## string

内置测试器，判断变量是否为字符串



## 自定义测试器template_test

使用app.template_test() 装饰器来自定义

```
@app.template_test()
def baz(n):
    if n == 'baz';
        return True
    return False
```



## message flash

### set方法

每一个flask 消息变量只会存在在一个block中

在secure.py 里面设置独一无二的key

```
SECRET_KEY = '123413141431341'
```



在web的路由函数下面，生成闪现消息

```
@web.route('/test')
def test():
    r = {
        'name': '',
        'age': 30
    }
    flash('Hello, Rick')
    return render_template('test.html', data=r)
```



在模版文件中调用

```
{% extends 'layout.html' %}
{% block content %}
    {{ super() }}
    {{ data.age }}
    {% if data.age < 18 %}
        {{ data.name }}
    {% elif data.age == 30 %}
        {{ data.school | default('Unnamed') }}
        {% set messages = get_flashed_messages() %}
        {{ messages }}
    {% else %}
        {{ data.age }}
    {% endif %}
{% endblock %}
```



在模板中定义变量，使用set标签

```
{% set navigation = [('/', 'Home'), ('/about', 'About')] %}

{% set navigation %}
	<li><a href="/">Home</a></li>
	<li><a href="/about">About</a></li>
{% endset %}
```



## 



### with方法

消息只会在with之间有效果

相比较于set，with需要语法闭合

```
{% extends 'layout.html' %}
{% block content %}
    {{ super() }}
    {{ data.age }}
    {% if data.age < 18 %}
        {{ data.name }}
    {% elif data.age == 30 %}
        {{ data.school | default('Unnamed') }}
        {% with messages = get_flashed_messages() %}
        {{ messages }}
        {% endwith %}
    {% else %}
        {{ data.age }}
    {% endif %}
{% endblock %}
```



# 全局上下文变量

内置的上下文变量，可以在模板中直接使用

| 变量    | 说明                                           |
| ------- | ---------------------------------------------- |
| config  | 当前的配置对象                                 |
| request | 当前的请求对象，在已激活的请求环境下可用       |
| session | 当前的会话对象，在已激活的请求环境下可用       |
| g       | 与请求绑定的全局变量，在已激活的请求环境下可用 |



## for 循环

```
{% for movie in movies %}
	<li>{{ movie.name }} - {{ movie.year }}</li>
{% endfor %}
```





## 常用循环变量

| 变量名          | 说明                         |
| --------------- | ---------------------------- |
| loop.index      | 当前迭代数，从1开始计数      |
| loop.index()    | 当前迭代数，从0开始计数      |
| loop.revindex   | 当前反向迭代数，从1开始计数  |
| loop.revindex() | 当前反向迭代数，从0开始计数  |
| loop.first      | 如果是第一个元素，则为True   |
| loop.last       | 如果是最后一个元素，则为True |
| loop.previtem   | 上一个迭代的条目             |
| loop.nextitem   | 下一个迭代的条目             |
| loop.length     | 序列包含的元素数量           |





