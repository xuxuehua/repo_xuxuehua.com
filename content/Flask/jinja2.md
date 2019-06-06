---
title: "jinja2"
date: 2019-06-02 16:21
---


[TOC]



# jinja2



## 模版继承

```
{% extends 'layout.html' %}
{% block content %}
    {{ super() }}
    {{ data.age }}
    {% if data.age < 18 %}
        {{ data.name }}
    {% elif data.age == 30 %}
        I am 30 years old.
    {% else %}
        {{ data.age }}
    {% endif %}
{% endblock %}
```

> 这里的super() 会实现继承效果





## 过滤器



### default

类似于管道命令

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



### length

求长度

```
{{ data | length() }}
```

> 获取到data的长度信息





## url_for 函数

通过相对固定的endpoint来构建URL地址， 即生成URL地址

```
 <link rel="stylesheet" href="{{ url_for('static', filename='test.css') }}">
```

> static 是endpoint地址





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

