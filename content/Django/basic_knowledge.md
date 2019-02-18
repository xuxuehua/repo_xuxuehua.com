---
title: "basic_knowledge"
date: 2018-10-27 00:52
---


[TOC]


# basic_knowledge

## 简介

Django是一种基于Python的Web开发框架。

Django本身基于MVC模型，即Model（模型）+View（视图）+ Controller（控制器）设计模式，因此天然具有MVC的出色基因：开发快捷、部署方便、可重用性高、维护成本低等。Python加Django是快速开发、设计、部署网站的最佳组合。



### 特点

- 功能完善、要素齐全：该有的、可以没有的都有，常用的、不常用的工具都用。Django提供了大量的特性和工具，无须你自己定义、组合、增删及修改。但是，在有些人眼里这被认为是‘臃肿’不够灵活，发挥不了程序员的主动能力。（一体机和DIY你更喜欢哪个？^-^）
- 完善的文档：经过十多年的发展和完善，Django有广泛的实践经验和完善的在线文档（可惜大多数为英文）。开发者遇到问题时可以搜索在线文档寻求解决方案。
- 强大的数据库访问组件：Django的Model层自带数据库ORM组件，使得开发者无须学习其他数据库访问技术（SQL、pymysql、SQLALchemy等）。当然你也可以不用Django自带的ORM，而是使用其它访问技术，比如SQLALchemy。
- 灵活的URL映射：Django使用正则表达式管理URL映射，灵活性高。
- 丰富的Template模板语言：类似jinjia模板语言，不但原生功能丰富，还可以自定义模板标签。
- 自带免费的后台管理系统：只需要通过简单的几行配置和代码就可以实现一个完整的后台数据管理控制平台。
- 完整的错误信息提示：在开发调试过程中如果出现运行错误或者异常，Django可以提供非常完整的错误信息帮助定位问题。



### MVC设计模式

最早由`Trygve Teenskaug`在1978年提出，上世纪80年代是程序语言Smalltalk的一种内部架构。后来MVC被其他领域借鉴，成为了软件工程中的一种软件架构模式。MVC把Web框架分为3个基础部分：

**模型(Model)**：用于封装与应用程序的业务逻辑相关的数据及对数据的处理方法，是Web应用程序中用于处理应用程序的数据逻辑的部分，Model只提供功能性的接口，通过这些接口可以获取Model的所有功能。白话说，这个模块就是Web框架和数据库的交互层。

**视图(View)**：负责数据的显示和呈现，是对用户的直接输出。

**控制器(Controller)**：负责从用户端收集用户的输入，可以看成提供View的反向功能。



### MTV设计模式

Django对传统的MVC设计模式进行了修改，将视图分成View模块和Template模块两部分，将动态的逻辑处理与静态的页面展现分离开。而Model采用了ORM技术，将关系型数据库表抽象成面向对象的Python类，将表操作转换成类操作，避免了复杂的SQL语句编写。MTV和MVC本质上是一样的。

**模型(Model)**：和MVC中的定义一样

**模板(Template)**：将数据与HTML语言结合起来的引擎

**视图(View)**：负责实际的业务逻辑实现





## 安装

Django对Python版本的依赖关系如下表所示：

| Django 版本 | Python 版本                                     |
| ----------- | ----------------------------------------------- |
| 1.8         | 2.7, 3.2 (until the end of 2016), 3.3, 3.4, 3.5 |
| 1.9, 1.10   | 2.7, 3.4, 3.5                                   |
| 1.11        | 2.7, 3.4, 3.5, 3.6                              |
| 2.0         | 3.4, 3.5, 3.6                                   |
| 2.1         | 3.5, 3.6, 3.7                                   |

```
pip install django==1.11
```



### 测试Django

创建一个叫做mysite的Django项目

```
django-admin startproject mysite
```

在mysite根目录中，又有一个mysite目录，这是整个项目的配置文件目录（一定不要和同名的根目录搞混淆了），还有一个manage.py文件，是整个项目的管理脚本。



Django会以`127.0.0.1:8000`这个默认配置启动开发服务器

```
Python manage.py runserver
```

```
python manage.py runserver 0.0.0.0:8000
```



### 项目结构

```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

> 外层的`mysite/`目录与Django无关，只是你项目的容器，可以任意命名。
>
> `manage.py`：一个命令行工具，用于与Django进行不同方式的交互脚本，非常重要！
>
> 内层的`mysite/`目录是真正的项目文件包裹目录，它的名字是你引用内部文件的包名，例如：`mysite.urls`。
> `mysite/__init__.py`:一个定义包的空文件。
> `mysite/settings.py`:项目的主配置文件，非常重要！
> `mysite/urls.py`:路由文件，所有的任务都是从这里开始分配，相当于Django驱动站点的内容表格，非常重要！
> `mysite/wsgi.py`:一个基于WSGI的web服务器进入点，提供底层的网络通信功能，通常不用关心。



## 第一个APP

### 应用APP

app应用与project项目的区别：

- 一个app实现某个功能，比如博客、公共档案数据库或者简单的投票系统；
- 一个project是配置文件和多个app的集合，这些app组合成整个站点；
- 一个app可以属于多个project！

app的存放位置可以是任何地点，但是通常都将它们放在与`manage.py`脚本同级的目录下，这样方便导入文件。



#### 生成app

```
$ python manage.py startapp polls
```





### 视图views

`polls/views.py`文件中，编写代码：

```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```



### 路由路径

在polls目录中新建一个文件，名字为`urls.py`，在其中输入代码如下：

```
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

> 除了admin路由外，尽量给每个app设计自己独立的二级路由。



#### 导入主urls

在项目的**主urls文件**中添加`urlpattern`条目，指向我们刚才建立的polls这个app独有的urls文件，这里需要导入include模块。打开`mysite/urls.py`文件，代码如下：

```
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]
```

> include语法相当于多级路由，它把接收到的url地址去除前面的正则表达式，将剩下的字符串传递给下一级路由进行判断。



#### url()方法

url()方法可以接收4个参数，其中2个是必须的：`regex`和`view`，以及2个可选的参数：`kwargs`和`name`



### 数据库操作

数据库中创建这些表

```
python manage.py migrate
```

> migrate命令将遍历`INSTALLED_APPS`设置中的所有项目，在数据库中创建对应的表，并打印出每一条动作信息。
>
> `python manage.py migrate`，将操作同步到数据库



### 创建模型

定义模型model，模型本质上就是数据库表的布局，再附加一些元数据



创建两个模型：`Question`和`Choice`。Question包含一个问题和一个发布日期。Choice包含两个字段：该选项的文本描述和该选项的投票数。每一条Choice都关联到一个Question。

```
# polls/models.py

from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```



* 显示一些我们指定的信息

```
from django.db import models
from django.utils.encoding import python_2_unicode_compatible

@python_2_unicode_compatible # 当你想支持python2版本的时候才需要这个装饰器
class Question(models.Model):
    # ...
    def __str__(self):   # 在python2版本中使用的是__unique__
        return self.question_text

@python_2_unicode_compatible 
class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```



#### 自定义模型

用于判断问卷是否最近时间段内发布度的：

```
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```



#### 启用模型

需要在`INSTALLED_APPS`设置中增加指向该应用的配置文件的链接。对于本例的投票应用，它的配置类文件是`polls/apps.py`，路径格式为`polls.apps.PollsConfig`。我们需要在`INSTALLED_APPS`中，将该路径添加进去：

```
# mysite/settings.py

INSTALLED_APPS = [
'polls.apps.PollsConfig',
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
]
```

实际上，在多数情况下，我们简写成‘polls’就可以了：

```
# mysite/settings.py

INSTALLED_APPS = [
'polls',
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
]
```



加入项目

```
$ python manage.py makemigrations polls
```

> 运行`makemigrations`命令，相当于告诉Django你对模型有改动，并且你想把这些改动保存为一个“迁移(migration)”。
>
> `python manage.py makemigrations`为改动创建迁移记录





看到类似下面的提示：

```
Migrations for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
```



运行`python manage.py check`命令，它将检查项目中的错误，并不实际进行迁移或者链接数据库的操作



### admin后台

创建一个可以登录admin站点的用户：

```
$ python manage.py createsuperuser
```

输入用户名：

```
Username: admin
```

输入邮箱地址：

```
Email address: xxx@xxx.xxx
```

输入密码：

```
Password: **********
Password (again): *********
Superuser created successfully.
```



#### 修改admin路径

打开根url路由文件`mysite/urls.py`，修改其中admin.site.urls对应的正则表达式，换成你想要的，比如：

```
from django.conf.urls import url
from django.contrib import admin

urlpatterns = [
    url(r'^my/set/', admin.site.urls),
]
```

> 访问`http://127.0.0.1:8000/my/set/`才能进入admin界面



#### admin注册应用

现在还无法看到投票应用，必须先在admin中进行注册，告诉admin站点，请将polls的模型加入站点内，接受站点的管理。

打开`polls/admin.py`文件，加入下面的内容：

```
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```





### 编写视图

下面，打开`polls/views.py`文件，输入下列代码：

```
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

然后，在`polls/urls.py`文件中加入下面的url模式，将其映射到我们上面新增的视图。

```
from django.conf.urls import url
from . import views

urlpatterns = [
    # ex: /polls/
    url(r'^$', views.index, name='index'),
    # ex: /polls/5/
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    # ex: /polls/5/results/
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    # ex: /polls/5/vote/
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```





#### 新的index()视图

用于替代先前无用的index，它会根据发布日期显示最近的5个投票问卷。

```
from django.http import HttpResponse

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```



### 模板文件

在polls目录下创建一个新的`templates`目录，Django会在它里面查找模板文件。在templates目录中，再创建一个新的子目录名叫`polls`，进入该子目录，创建一个新的html文件`index.html`。换句话说，你的模板文件应该是`polls/templates/polls/index.html`。可以在DJango中直接使用`polls/index.html`引用该文件。



写入文件`polls/templates/polls/index.html`:

```
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
    <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```



修改视图文件`polls/views.py`，让新的`index.html`文件生效：

```
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
    'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```



修改视图文件`polls/views.py`，让新的`index.html`文件生效：

```
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
    'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```



#### render 函数

加载模板、传递参数，返回HttpResponse对象是一整套再常用不过的操作了，为了节省力气，Django提供了一个快捷方式：render函数，一步到位！看如下代码：

polls/views.py

```
from django.shortcuts import render
from .models import Question
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

> render()函数的第一个位置参数是请求对象（就是view函数的第一个参数），第二个位置参数是模板。还可以有一个可选的第三参数，一个字典，包含需要传递给模板的数据。最后render函数返回一个经过字典数据渲染过的模板封装而成的HttpResponse对象。



#### 404 页面

视图`polls/views.py`：

```
from django.http import Http404
from django.shortcuts import render
from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```



get_object_or_404()

就像render函数一样，Django同样为你提供了一个偷懒的方式，替代上面的多行代码，那就是`get_object_or_404()`方法，参考下面的代码：

polls/views.py

```
from django.shortcuts import get_object_or_404, render
from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```



### 模板系统

`detail()`视图会将上下文变量question传递给对应的`polls/templates/polls/detail.html`模板，修改该模板的内容，如下所示：

```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

在模板系统中圆点`.`是万能的魔法师，你可以用它访问对象的属性。在例子`{{ question.question_text }}`中，DJango首先会在question对象中尝试查找一个字典，如果失败，则尝试查找属性，如果再失败，则尝试作为列表的索引进行查询。

在 `{% for %}`循环中的方法调用——`question.choice_set.all`其实就是Python的代码`question.choice_set.all()`,它将返回一组可迭代的`Choice`对象，并用在`{% for %}`标签中。



#### 删除模板中硬编码的URLs

在`polls/index.html`文件中，还有一部分硬编码存在，也就是href里的“/polls/”部分：

```
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

它对于代码修改非常不利。设想如果你在urls.py文件里修改了正则表达式，那么你所有的模板中对这个url的引用都需要修改，这是无法接受的！

我们前面给urls定义了一个name别名，可以用它来解决这个问题。具体代码如下：

```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

Django会在`polls.urls`文件中查找`name='detail'`的url，具体的就是下面这行：

```
url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
```

举个栗子，如果你想将polls的detail视图的URL更换为`polls/specifics/12/`，那么你不需要在模板中重新修改url地址了，仅仅只需要在`polls/urls.py`文件中，将对应的正则表达式改成下面这样的就行了，所有模板中对它的引用都会自动修改成新的链接：

```
# 添加新的单词'specifics'
url(r'^specifics/(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
```



### URL names的命名空间

只有一个app也就是polls，但是在现实中很显然会有5个、10个、更多的app同时存在一个项目中。Django是如何区分这些app之间的URL name呢？

答案是使用URLconf的命名空间。在polls/urls.py文件的开头部分，添加一个`app_name`的变量来指定该应用的命名空间：

```
from django.conf.urls import url
from . import views

app_name = 'polls'  # 关键是这行
urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```

现在，让我们将代码修改得更严谨一点，将`polls/templates/polls/index.html`中的

```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

修改为：

```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```



### 表单form

为了接收用户的投票选择，我们需要在前端页面显示一个投票界面。让我们重写先前的`polls/detail.html`文件，代码如下：

```
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
{% endfor %}
<input type="submit" value="Vote" />
</form>
```

> - 上面的模板显示一系列单选按钮，按钮的值是选项的ID，按钮的名字是字符串"choice"。这意味着，当你选择了其中某个按钮，并提交表单，一个包含数据`choice=#`的POST请求将被发送到指定的url，`#`是被选择的选项的ID。这就是HTML表单的基本概念。
> - 如果你有一定的前端开发基础，那么form标签的action属性和method属性你应该很清楚它们的含义，action表示你要发送的目的url，method表示提交数据的方式，一般分POST和GET。
> - forloop.counter是DJango模板系统专门提供的一个变量，用来表示你当前循环的次数，一般用来给循环项目添加有序数标。
> - 由于我们发送了一个POST请求，就必须考虑一个跨站请求伪造的安全问题，简称CSRF（具体含义请百度）。Django为你提供了一个简单的方法来避免这个困扰，那就是在form表单内添加一条{% csrf_token %}标签，标签名不可更改，固定格式，位置任意，只要是在form表单内。这个方法对form表单的提交方式方便好使，但如果是用ajax的方式提交数据，那么就不能用这个方法了。



vote视图函数（polls/views.py）

```
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse
from .models import Choice, Question
# ...

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # 发生choice未找到异常时，重新返回表单页面，并给出提示信息
        return render(request, 'polls/detail.html', {
        'question': question,
        'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # 成功处理数据后，自动跳转到结果页面，防止用户连续多次提交。
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

> - request.POST是一个类似字典的对象，允许你通过键名访问提交的数据。本例中，`request.POST[’choice’]`返回被选择选项的ID，并且值的类型永远是string字符串，那怕它看起来像数字！同样的，你也可以用类似的手段获取GET请求发送过来的数据，一个道理。
> - `request.POST[’choice’]`有可能触发一个KeyError异常，如果你的POST数据里没有提供choice键值，在这种情况下，上面的代码会返回表单页面并给出错误提示。PS：通常我们会给个默认值，防止这种异常的产生，例如`request.POST[’choice’,None]`，一个None解决所有问题。
> - 在选择计数器加一后，返回的是一个`HttpResponseRedirect`而不是先前我们常用的`HttpResponse`。HttpResponseRedirect需要一个参数：重定向的URL。这里有一个建议，当你成功处理POST数据后，应当保持一个良好的习惯，始终返回一个HttpResponseRedirect。这不仅仅是对Django而言，它是一个良好的WEB开发习惯。
> - 我们在上面HttpResponseRedirect的构造器中使用了一个`reverse()`函数。它能帮助我们避免在视图函数中硬编码URL。它首先需要一个我们在URLconf中指定的name，然后是传递的数据。例如`'/polls/3/results/'`，其中的3是某个`question.id`的值。重定向后将进入`polls:results`对应的视图，并将`question.id`传递给它。白话来讲，就是把活扔给另外一个路由对应的视图去干。



当有人对某个问题投票后，vote()视图重定向到了问卷的结果显示页面。下面我们来写这个处理结果页面的视图(polls/views.py)：

```
from django.shortcuts import get_object_or_404, render

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```

同样，还需要写个模板`polls/templates/polls/results.html`。（路由、视图、模板、模型！都是这个套路....）

```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>
<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```

现在你可以到浏览器中访问`/polls/1/`了，投票吧。你会看到一个结果页面，每投一次，它的内容就更新一次。如果你提交的时候没有选择项目，则会得到一个错误提示。



### 类视图

上面的detail、index和results视图的代码非常相似，有点冗余，这是一个程序猿不能忍受的。他们都具有类似的业务逻辑，实现类似的功能：通过从URL传递过来的参数去数据库查询数据，加载一个模板，利用刚才的数据渲染模板，返回这个模板。由于这个过程是如此的常见，Django很善解人意的帮你想办法偷懒，于是它提供了一种快捷方式，名为“类视图”。

让我们来试试看将原来的代码改为使用类视图的方式，整个过程分三步走：

- 修改URLconf设置
- 删除一些旧的无用的视图
- 采用基于类视图的新视图



#### 改良URLconf

打开`polls/urls.py`文件，将其修改成下面的样子：

```
from django.conf.urls import url
from . import views

app_name = 'polls'
urlpatterns = [
    url(r'^$', views.IndexView.as_view(), name='index'),
    url(r'^(?P<pk>[0-9]+)/$', views.DetailView.as_view(), name='detail'),
    url(r'^(?P<pk>[0-9]+)/results/$', views.ResultsView.as_view(), name='results'),
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```

请注意：在上面的的第2,3条目中将原来的`<question_id>`修改成了`<pk>`.

#### 修改视图

接下来，打开`polls/views.py`文件，删掉index、detail和results视图，替换成Django的类视图，如下所示：

```
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.views import generic
from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'
    def get_queryset(self):
    """返回最近发布的5个问卷."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name ='polls/results.html'


def vote(request, question_id):
... # 这个视图未改变！！！
```

在这里，我们使用了两种类视图`ListView`和`DetailView`（它们是作为父类被继承的）。这两者分别代表“显示一个对象的列表”和“显示特定类型对象的详细页面”的抽象概念。

- 每一种类视图都需要知道它要作用在哪个模型上，这通过model属性提供。
- `DetailView`类视图需要从url捕获到的称为"pk"的主键值，因此我们在url文件中将2和3条目的`<question_id>`修改成了`<pk>`。



### 静态文件

除了由服务器生成的HTML文件外，WEB应用一般需要提供一些其它的必要文件，比如图片文件、JavaScript脚本和CSS样式表等等，用来为用户呈现出一个完整的网页。在Django中，我们将这些文件统称为“静态文件”



这个css样式文件应该是`polls/static/polls/style.css`。你可以通过书写`polls/style.css`在Django中访问这个静态文件，与你如何访问模板的路径类似。



**良好的目录结构是每个应用都应该创建自己的urls、views、models、templates和static，每个templates包含一个与应用同名的子目录，每个static也包含一个与应用同名的子目录。**

将下面的代码写入样式文件`polls/static/polls/style.css`：

```
li a {
    color: green;
}
```

接下来在模板文件`polls/templates/polls/index.html`的头部加入下面的代码：

```
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```

`{% static %}`模板标签会生成静态文件的绝对URL路径。

在浏览器访问`http://localhost:8000/polls/`，你会看到Question的超级链接变成了绿色（Django风格！），这意味着你的样式表被成功导入了。



#### 背景图片

下面，我们在`polls/static/polls/`目录下创建一个用于存放图片的`images`子目录，在这个子目录里放入`background.gif文件。换句话说，这个文件的路径是polls/static/polls/images/background.gif。(你可以使用任何你想要的图片)

在css样式文件`polls/static/polls/style.css`中添加下面的代码：

```
body {
    background: white url("images/background.gif") no-repeat right bottom;
}
```

重新加载`http://localhost:8000/polls/`(CTRL+F5或者直接F5)，你会在屏幕的右下方看到载入的背景图片。



### 自定义admin站点