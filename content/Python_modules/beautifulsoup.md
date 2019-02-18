---
title: "beautifulsoup"
date: 2018-07-29 01:35
---

[TOC]



# bs4 模块

BeautifulSoup 也是一个HTML/XML 解释器。

相对于lxml只会局部遍历，BeautifulSoup是基于HTML DOM(Document Object Model)， 会加载整个文档，解析整个DOM树，因此时间和内存开销都会大很多

支持CSS选择器，XML解析器



## 安装 

```
pip install bs4
```



## 解析器

推荐使用lxml，效率更高

```
pip install lxml
```

html5lib 可以自动补全标签

```
pip install html5lib
```



| 解析器           | 使用方法                                                     | 优势                                                  | 劣势                                            |
| ---------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ----------------------------------------------- |
| Python标准库     | `BeautifulSoup(markup, "html.parser")`                       | Python的内置标准库执行速度适中文档容错能力强          | Python 2.7.3 or 3.2.2)前 的版本中文档容错能力差 |
| lxml HTML 解析器 | `BeautifulSoup(markup, "lxml")`                              | 速度快文档容错能力强                                  | 需要安装C语言库                                 |
| lxml XML 解析器  | `BeautifulSoup(markup, ["lxml", "xml"])``BeautifulSoup(markup, "xml")` | 速度快唯一支持XML的解析器                             | 需要安装C语言库                                 |
| html5lib         | `BeautifulSoup(markup, "html5lib")`                          | 最好的容错性以浏览器的方式解析文档生成HTML5格式的文档 | 速度慢不依赖外部扩展                            |



# 对象的种类

Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构,每个节点都是Python对象,所有对象可以归纳为4种: `Tag` , `NavigableString` , `BeautifulSoup` , `Comment` .



## Tag

BeautifulSoup 中所有的标签都是tag类型，并且BeautifulSoup的对象其实本质上也是一个Tag类型。

所以find，find_all并不是BeatifulSoup的，而是Tag的



```
soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
tag = soup.b
type(tag)
# <class 'bs4.element.Tag'>
```



### Name

每个tag都有自己的名字,通过 `.name` 来获取:

```
tag.name
# u'b'
```

如果改变了tag的name,那将影响所有通过当前Beautiful Soup对象生成的HTML文档:

```
tag.name = "blockquote"
tag
# <blockquote class="boldest">Extremely bold</blockquote>
```

### Attributes

一个tag可能有很多个属性. tag `<b class="boldest">` 有一个 “class” 的属性,值为 “boldest” . tag的属性的操作方法与字典相同:

```
tag['class']
# u'boldest'
```

也可以直接”点”取属性, 比如: `.attrs` :

```
tag.attrs
# {u'class': u'boldest'}
```

tag的属性可以被添加,删除或修改. 再说一次, tag的属性操作方法与字典一样

```
tag['class'] = 'verybold'
tag['id'] = 1
tag
# <blockquote class="verybold" id="1">Extremely bold</blockquote>

del tag['class']
del tag['id']
tag
# <blockquote>Extremely bold</blockquote>

tag['class']
# KeyError: 'class'
print(tag.get('class'))
# None
```



#### 多值属性

最常见的多值的属性是 class (一个tag可以有多个CSS的class)

在Beautiful Soup中多值属性的返回类型是list:

```
css_soup = BeautifulSoup('<p class="body strikeout"></p>')
css_soup.p['class']
# ["body", "strikeout"]

css_soup = BeautifulSoup('<p class="body"></p>')
css_soup.p['class']
# ["body"]
```





## NavigableString （字符串遍历）

字符串常被包含在tag内.Beautiful Soup用 `NavigableString` 类来包装tag中的字符串

继承自Python中的str，使用起来和stg是一样的

### string

获取当前某个标签下的非标签字符串

```
tag.string
```



### strings 

获取某个标签下的子孙非标签字符串

```
trs = soup.find_all('tr')
for tr in trs:
    infos = list(tr.strings)
```



### stripped_strings

获取某个标签下的子孙非标签字符串，会去掉空白字符

```
trs = soup.find_all('tr')
for tr in trs:
    infos = list(tr.stripped_strings)
```







## BeautifulSoup

 `BeautifulSoup` 对象并不是真正的HTML或XML的tag,所以它没有name和attribute属性.但有时查看它的 `.name` 属性是很方便的,所以 `BeautifulSoup` 对象包含了一个值为 “[document]” 的特殊属性 `.name`

继承自Tag，用来生成BeautifulSoup 树，对于find_all，select查找方法，其实还是Tag的

```
soup.name
# u'[document]'
```



## Comment

`Tag` , `NavigableString` , `BeautifulSoup` 几乎覆盖了html和xml中的所有内容,但是还有一些特殊对象.容易让人担心的内容是文档的注释部分:

继承自NavigableString

```
markup = "<b><!--Hey, buddy. Want to buy a used parser?--></b>"
soup = BeautifulSoup(markup)
comment = soup.b.string
type(comment)
# <class 'bs4.element.Comment'>
```





# 遍历HTML

html 文件

```
html_doc = """
<html><head><title>The Dormouse's story</title></head>

<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
```



## 通过tag的名字

操作文档树最简单的方法就是告诉它你想获取的tag的name.如果想获取 <head> 标签,只要用 `soup.head` :

```
soup.head
# <head><title>The Dormouse's story</title></head>

soup.title
# <title>The Dormouse's story</title>
```



## .parent

通过 `.parent` 属性来获取某个元素的父节点.在例子“爱丽丝”的文档中,<head>标签是<title>标签的父节点:

```
title_tag = soup.title
title_tag
# <title>The Dormouse's story</title>
title_tag.parent
# <head><title>The Dormouse's story</title></head>
```

文档的顶层节点比如<html>的父节点是 `BeautifulSoup` 对象:

```
html_tag = soup.html
type(html_tag.parent)
# <class 'bs4.BeautifulSoup'>
```

`BeautifulSoup` 对象的 `.parent` 是None:

```
print(soup.parent)
# None
```



## .parents

通过元素的 `.parents` 属性可以递归得到元素的所有父辈节点,下面的例子使用了 `.parents` 方法遍历了<a>标签到根节点的所有节点.

```
link = soup.a
link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
for parent in link.parents:
    if parent is None:
        print(parent)
    else:
        print(parent.name)
# p
# body
# html
# [document]
# None
```





## .contents

tag的 `.contents` 属性可以将tag的子节点以列表的方式输出:

```
head_tag = soup.head
head_tag
# <head><title>The Dormouse's story</title></head>

head_tag.contents
[<title>The Dormouse's story</title>]

title_tag = head_tag.contents[0]
title_tag
# <title>The Dormouse's story</title>
title_tag.contents
# [u'The Dormouse's story']
```



## .children

通过tag的 `.children` 生成器,可以对tag的子节点进行循环:

```
for child in title_tag.children:
    print(child)
    # The Dormouse's story
```



## .next_sibling 

在文档树中,使用 `.next_sibling` 和 `.previous_sibling` 属性来查询兄弟节点:

```
sibling_soup.b.next_sibling
# <c>text2</c>
```

实际文档中的tag的 `.next_sibling` 和 `.previous_sibling` 属性通常是字符串或空白

```
link = soup.a
link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

link.next_sibling
# u',\n'
```



通过 `.next_siblings` 和 `.previous_siblings` 属性可以对当前节点的兄弟节点迭代输出:

```
for sibling in soup.a.next_siblings:
    print(repr(sibling))
    # u',\n'
    # <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
    # u' and\n'
    # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
    # u'; and they lived at the bottom of a well.'
    # None

```



## .previous_sibling

在文档树中,使用 `.next_sibling` 和 `.previous_sibling` 属性来查询兄弟节点:

```
sibling_soup.c.previous_sibling
# <b>text1</b>
```

通过 `.next_siblings` 和 `.previous_siblings` 属性可以对当前节点的兄弟节点迭代输出:

```
for sibling in soup.find(id="link3").previous_siblings:
    print(repr(sibling))
    # ' and\n'
    # <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
    # u',\n'
    # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
    # u'Once upon a time there were three little sisters; and their names were\n'
    # None
```



## .next_element 

`.next_element` 属性指向解析过程中下一个被解析的对象(字符串或tag),结果可能与 `.next_sibling` 相同,但通常是不一样的.

```
last_a_tag = soup.find("a", id="link3")
last_a_tag
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

last_a_tag.next_sibling
# '; and they lived at the bottom of a well.'
```



## .previous_element

`.previous_element` 属性刚好与 `.next_element` 相反,它指向当前被解析的对象的前一个解析对象

```
last_a_tag.previous_element
# u' and\n'
last_a_tag.previous_element.next_element
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
```



## .next_elements

通过 `.next_elements` 的迭代器就可以向前或向后访问文档的解析内容

```
for element in last_a_tag.next_elements:
    print(repr(element))
# u'Tillie'
# u';\nand they lived at the bottom of a well.'
# u'\n\n'
# <p class="story">...</p>
# u'...'
# u'\n'
# None
```



##  .previous_elements

`.previous_elements` 就好像文档正在被解析一样






# 搜索HTML

## find

find( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

找到第一个标签并返回，只会返回一个元素

使用 `find_all` 方法并设置 `limit=1` 参数不如直接使用 `find()` 方法.下面两行代码是等价的:

```
soup.find_all('title', limit=1)
# [<title>The Dormouse's story</title>]

soup.find('title')
# <title>The Dormouse's story</title>
```



`find()` 方法找不到目标时,返回 `None` .

```
print(soup.find("nosuchtag"))
# None
```





## find_all

find_all( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_all()` 方法搜索当前tag的所有tag子节点,并判断是否符合过滤器的条件

找到所有的标签并返回，以列表形式

```
In [5]: import requests

In [6]: from bs4 import BeautifulSoup

In [7]: url = 'http://www.itest.info/courses'
In [8]: soup = BeautifulSoup(requests.get(url).text, 'html.parser')

In [9]: for course in soup.find_all('h4'):
   ...:     print(course.text)
   ...:
Android测试开发（Java语言）
性能测试从入门到精通班
接口自动化测试开发--Python班
Selenium自动化测试--Java班
Selenium自动化测试--Python班
```



```
<span class="item_hot_topic_title">
  <a href="/t/415664">冬天了，身上静电咋处理？</a>
</span>
```

```
for span in soup.find_all('span', class_='item_hot_topic_title'):
    print(span.find('a').text, span.find('a')['href'])
```

> `for span in soup.find_all('span', class_='item_hot_topic_title')`: 遍历所有的class=item_hot_topic_title的span。注意是`class_`，不是`class`，因为class是python的关键字，所以后面要加个尾巴，防止冲突



### attrs

通过attrs参数将属性条件返回到字典中

```
data_soup.find_all(attrs={"data-foo": "value"})
# [<div data-foo="value">foo!</div>]
```

```
soup.find_all("a", attrs={"class": "sister"})
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```



### 列表搜索

如果传入列表参数,Beautiful Soup会将与列表中任一元素匹配的内容返回.下面代码找到文档中所有<a>标签和<b>标签:

```
soup.find_all(["a", "b"])
# [<b>The Dormouse's story</b>,
#  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```



### True

`True` 可以匹配任何值,下面代码查找到所有的tag,但是不会返回字符串节点

```
for tag in soup.find_all(True):
    print(tag.name)
# html
# head
# title
# body
# p
# b
# p
# a
# a
# a
# p
```



### CSS搜索

从Beautiful Soup的4.1.1版本开始,可以通过 `class_` 参数搜索有指定CSS类名的tag

```
soup.find_all("a", class_="sister")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```



### text 参数

通过 `text` 参数可以搜搜文档中的字符串内容.与 `name` 参数的可选值一样, `text` 参数接受 [字符串](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id27) , [正则表达式](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id28) , [列表](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id29), True

```
soup.find_all(text="Elsie")
# [u'Elsie']

soup.find_all(text=["Tillie", "Elsie", "Lacie"])
# [u'Elsie', u'Lacie', u'Tillie']

soup.find_all(text=re.compile("Dormouse"))
[u"The Dormouse's story", u"The Dormouse's story"]

def is_the_only_string_within_a_tag(s):
    ""Return True if this string is the only child of its parent tag.""
    return (s == s.parent.string)

soup.find_all(text=is_the_only_string_within_a_tag)
# [u"The Dormouse's story", u"The Dormouse's story", u'Elsie', u'Lacie', u'Tillie', u'...']
```

```
soup.find_all("a", text="Elsie")
# [<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>]
```



### limit 参数

`find_all()` 方法返回全部的搜索结构,如果文档树很大那么搜索会很慢.如果我们不需要全部结果,可以使用 `limit` 参数限制返回结果的数量.效果与SQL中的limit关键字类似,当搜索到的结果数量达到 `limit` 的限制时,就停止搜索返回结果.

文档树中有3个tag符合搜索条件,但结果只返回了2个,因为我们限制了返回数量:

```
soup.find_all("a", limit=2)
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```



### recursive 参数

调用tag的 `find_all()` 方法时,Beautiful Soup会检索当前tag的所有子孙节点,如果只想搜索tag的直接子节点,可以使用参数 `recursive=False` .

一段简单的文档:

```
<html>
 <head>
  <title>
   The Dormouse's story
  </title>
 </head>
...
```

是否使用 `recursive` 参数的搜索结果:

```
soup.html.find_all("title")
# [<title>The Dormouse's story</title>]

soup.html.find_all("title", recursive=False)
# []
```





### 正则表达式

如果传入正则表达式作为参数,Beautiful Soup会通过正则表达式的 `match()` 来匹配内容.下面例子中找出所有以b开头的标签,这表示<body>和<b>标签都应该被找到:

```
import re
for tag in soup.find_all(re.compile("^b")):
    print(tag.name)
# body
# b
```

下面代码找出所有名字中包含”t”的标签:

```
for tag in soup.find_all(re.compile("t")):
    print(tag.name)
# html
# title
```



## find_parents() 和 find_parent()

find_parents( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

find_parent( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_parents()` 和 `find_parent()` 用来搜索当前节点的父辈节点,搜索方法与普通tag的搜索方法相同,搜索文档搜索文档包含的内容.

```
a_string = soup.find(text="Lacie")
a_string
# u'Lacie'

a_string.find_parents("a")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

a_string.find_parent("p")
# <p class="story">Once upon a time there were three little sisters; and their names were
#  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
#  and they lived at the bottom of a well.</p>

a_string.find_parents("p", class="title")
# []
```



## find_next_siblings() 合 find_next_sibling()

find_next_siblings( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

find_next_sibling( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_next_siblings()` 方法返回所有符合条件的后面的兄弟节点, `find_next_sibling()` 只返回符合条件的后面的第一个tag节点.

```
first_link = soup.a
first_link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

first_link.find_next_siblings("a")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

first_story_paragraph = soup.find("p", "story")
first_story_paragraph.find_next_sibling("p")
# <p class="story">...</p>
```



## find_previous_siblings() 和 find_previous_sibling()

find_previous_siblings( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

find_previous_sibling( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_previous_siblings()` 方法返回所有符合条件的前面的兄弟节点, `find_previous_sibling()` 方法返回第一个符合条件的前面的兄弟节点:

```
last_link = soup.find("a", id="link3")
last_link
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

last_link.find_previous_siblings("a")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

first_story_paragraph = soup.find("p", "story")
first_story_paragraph.find_previous_sibling("p")
# <p class="title"><b>The Dormouse's story</b></p>
```



## find_all_next() 和 find_next()

find_all_next( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

find_next( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_all_next()` 方法返回所有符合条件的节点, `find_next()` 方法返回第一个符合条件的节点:

```
first_link = soup.a
first_link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

first_link.find_all_next(text=True)
# [u'Elsie', u',\n', u'Lacie', u' and\n', u'Tillie',
#  u';\nand they lived at the bottom of a well.', u'\n\n', u'...', u'\n']

first_link.find_next("p")
# <p class="story">...</p>
```



## find_all_previous() 和 find_previous()

find_all_previous( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

find_previous( [name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) )

`find_all_previous()` 方法返回所有符合条件的节点, `find_previous()` 方法返回第一个符合条件的节点.

```
first_link = soup.a
first_link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

first_link.find_all_previous("p")
# [<p class="story">Once upon a time there were three little sisters; ...</p>,
#  <p class="title"><b>The Dormouse's story</b></p>]

first_link.find_previous("title")
# <title>The Dormouse's story</title>
```



## CSS选择器 select 方法

### 通过标签名称查找

 `select()` 方法中传入字符串参数,即可使用CSS选择器的语法找到tag



```
soup.select("title")
# [<title>The Dormouse's story</title>]

soup.select("p nth-of-type(3)")
# [<p class="story">...</p>]
```

通过tag标签逐层查找

```
soup.select("body a")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select("html head title")
# [<title>The Dormouse's story</title>]
```

找到某个tag标签下的直接子标签

```
soup.select("head > title")
# [<title>The Dormouse's story</title>]

soup.select("p > a")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select("p > a:nth-of-type(2)")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

soup.select("p > #link1")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

soup.select("body > a")
# []
```

找到兄弟节点标签:

```
soup.select("#link1 ~ .sister")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie"  id="link3">Tillie</a>]

soup.select("#link1 + .sister")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

### 通过CSS的类名查找

```
soup.select(".sister")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select("[class~=sister]")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

### 通过tag的id查找

```
soup.select("#link1")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

soup.select("a#link2")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```



### 组合查找

```
soup.select('p #link1')

```

```
<div class="line"> div tag </div>

soup.select('div.line')
```







### 通过是否存在某个属性来查找

```
soup.select('a[href]')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

### 通过属性的值来查找

```
soup.select('a[href="http://example.com/elsie"]')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

soup.select('a[href^="http://example.com/"]')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select('a[href$="tillie"]')
# [<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select('a[href*=".com/el"]')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]
```

### 通过语言设置来查找

```
multilingual_markup = """
 <p lang="en">Hello</p>
 <p lang="en-us">Howdy, y'all</p>
 <p lang="en-gb">Pip-pip, old fruit</p>
 <p lang="fr">Bonjour mes amis</p>
"""
multilingual_soup = BeautifulSoup(multilingual_markup)
multilingual_soup.select('p[lang|=en]')
# [<p lang="en">Hello</p>,
#  <p lang="en-us">Howdy, y'all</p>,
#  <p lang="en-gb">Pip-pip, old fruit</p>]
```



# 修改HTML

Beautiful Soup的强项是文档树的搜索,但同时也可以方便的修改文档树



## 修改tag的名称和属性

在 [Attributes](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#attributes) 的章节中已经介绍过这个功能,但是再看一遍也无妨. 重命名一个tag,改变属性的值,添加或删除属性:

```
soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
tag = soup.b

tag.name = "blockquote"
tag['class'] = 'verybold'
tag['id'] = 1
tag
# <blockquote class="verybold" id="1">Extremely bold</blockquote>

del tag['class']
del tag['id']
tag
# <blockquote>Extremely bold</blockquote>
```



## 修改 .string

给tag的 `.string` 属性赋值,就相当于用当前的内容替代了原来的内容:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)

tag = soup.a
tag.string = "New link text."
tag
# <a href="http://example.com/">New link text.</a>
```

>  如果当前的tag包含了其它tag,那么给它的 `.string` 属性赋值会覆盖掉原有的所有内容包括子tag



## append()

`Tag.append()` 方法想tag中添加内容,就好像Python的列表的 `.append()` 方法:

```
soup = BeautifulSoup("<a>Foo</a>")
soup.a.append("Bar")

soup
# <html><head></head><body><a>FooBar</a></body></html>
soup.a.contents
# [u'Foo', u'Bar']
```



## BeautifulSoup.new_string() 和 .new_tag()

如果想添加一段文本内容到文档中也没问题,可以调用Python的 `append()` 方法或调用工厂方法 `BeautifulSoup.new_string()` :

```
soup = BeautifulSoup("<b></b>")
tag = soup.b
tag.append("Hello")
new_string = soup.new_string(" there")
tag.append(new_string)
tag
# <b>Hello there.</b>
tag.contents
# [u'Hello', u' there']
```

如果想要创建一段注释,或 `NavigableString` 的任何子类,将子类作为 `new_string()` 方法的第二个参数传入:

```
from bs4 import Comment
new_comment = soup.new_string("Nice to see you.", Comment)
tag.append(new_comment)
tag
# <b>Hello there<!--Nice to see you.--></b>
tag.contents
# [u'Hello', u' there', u'Nice to see you.']
```

\# 这是Beautiful Soup 4.2.1 中新增的方法

创建一个tag最好的方法是调用工厂方法 `BeautifulSoup.new_tag()` :

```
soup = BeautifulSoup("<b></b>")
original_tag = soup.b

new_tag = soup.new_tag("a", href="http://www.example.com")
original_tag.append(new_tag)
original_tag
# <b><a href="http://www.example.com"></a></b>

new_tag.string = "Link text."
original_tag
# <b><a href="http://www.example.com">Link text.</a></b>
```

第一个参数作为tag的name,是必填,其它参数选填

## insert()

`Tag.insert()` 方法与 `Tag.append()` 方法类似,区别是不会把新元素添加到父节点 `.contents` 属性的最后,而是把元素插入到指定的位置.与Python列表总的 `.insert()` 方法的用法下同:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
tag = soup.a

tag.insert(1, "but did not endorse ")
tag
# <a href="http://example.com/">I linked to but did not endorse <i>example.com</i></a>
tag.contents
# [u'I linked to ', u'but did not endorse', <i>example.com</i>]
```

## insert_before() 和 insert_after()

`insert_before()` 方法在当前tag或文本节点前插入内容:

```
soup = BeautifulSoup("<b>stop</b>")
tag = soup.new_tag("i")
tag.string = "Don't"
soup.b.string.insert_before(tag)
soup.b
# <b><i>Don't</i>stop</b>
```

`insert_after()` 方法在当前tag或文本节点后插入内容:

```
soup.b.i.insert_after(soup.new_string(" ever "))
soup.b
# <b><i>Don't</i> ever stop</b>
soup.b.contents
# [<i>Don't</i>, u' ever ', u'stop']
```

## clear()

`Tag.clear()` 方法移除当前tag的内容:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
tag = soup.a

tag.clear()
tag
# <a href="http://example.com/"></a>
```

## extract()

`PageElement.extract()` 方法将当前tag移除文档树,并作为方法结果返回:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
a_tag = soup.a

i_tag = soup.i.extract()

a_tag
# <a href="http://example.com/">I linked to</a>

i_tag
# <i>example.com</i>

print(i_tag.parent)
None
```

这个方法实际上产生了2个文档树: 一个是用来解析原始文档的 `BeautifulSoup` 对象,另一个是被移除并且返回的tag.被移除并返回的tag可以继续调用 `extract` 方法:

```
my_string = i_tag.string.extract()
my_string
# u'example.com'

print(my_string.parent)
# None
i_tag
# <i></i>
```

## decompose()

`Tag.decompose()` 方法将当前节点移除文档树并完全销毁:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
a_tag = soup.a

soup.i.decompose()

a_tag
# <a href="http://example.com/">I linked to</a>
```

## replace_with()

`PageElement.replace_with()` 方法移除文档树中的某段内容,并用新tag或文本节点替代它:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
a_tag = soup.a

new_tag = soup.new_tag("b")
new_tag.string = "example.net"
a_tag.i.replace_with(new_tag)

a_tag
# <a href="http://example.com/">I linked to <b>example.net</b></a>
```

`replace_with()` 方法返回被替代的tag或文本节点,可以用来浏览或添加到文档树其它地方

## wrap()

`PageElement.wrap()` 方法可以对指定的tag元素进行包装 [[8\]](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id89) ,并返回包装后的结果:

```
soup = BeautifulSoup("<p>I wish I was bold.</p>")
soup.p.string.wrap(soup.new_tag("b"))
# <b>I wish I was bold.</b>

soup.p.wrap(soup.new_tag("div"))
# <div><p><b>I wish I was bold.</b></p></div>
```

该方法在 Beautiful Soup 4.0.5 中添加

## unwrap()

`Tag.unwrap()` 方法与 `wrap()` 方法相反.将移除tag内的所有tag标签,该方法常被用来进行标记的解包:

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
a_tag = soup.a

a_tag.i.unwrap()
a_tag
# <a href="http://example.com/">I linked to example.com</a>
```

与 `replace_with()` 方法相同, `unwrap()` 方法返回被移除的tag



# Soup输出

## prettify

`prettify()` 方法将Beautiful Soup的文档树格式化后以Unicode编码输出,每个XML/HTML标签都独占一行

```
markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
soup = BeautifulSoup(markup)
soup.prettify()
# '<html>\n <head>\n </head>\n <body>\n  <a href="http://example.com/">\n...'

print(soup.prettify())
# <html>
#  <head>
#  </head>
#  <body>
#   <a href="http://example.com/">
#    I linked to
#    <i>
#     example.com
#    </i>
#   </a>
#  </body>
# </html>
```

`BeautifulSoup` 对象和它的tag节点都可以调用 `prettify()` 方法:

```
print(soup.a.prettify())
# <a href="http://example.com/">
#  I linked to
#  <i>
#   example.com
#  </i>
# </a>
```



## Str & Unicode输出

如果只想得到结果字符串,不重视格式,那么可以对一个 `BeautifulSoup` 对象或 `Tag` 对象使用Python的 `unicode()` 或 `str()` 方法:

```
str(soup)
# '<html><head></head><body><a href="http://example.com/">I linked to <i>example.com</i></a></body></html>'

unicode(soup.a)
# u'<a href="http://example.com/">I linked to <i>example.com</i></a>'
```

> `str()` 方法返回UTF-8编码的字符串,可以指定 [编码](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id51) 的设置.
>
> 还可以调用 `encode()` 方法获得字节码或调用 `decode()` 方法获得Unicode.



## get_text

获取某个标签下的子孙非标签字符串，不是以列表形式返回，以普通字符串返回 

如果只想得到tag中包含的文本内容,那么可以嗲用 `get_text()` 方法,这个方法获取到tag中包含的所有文版内容包括子孙tag中的内容,并将结果作为Unicode字符串返回:

```
markup = '<a href="http://example.com/">\nI linked to <i>example.com</i>\n</a>'
soup = BeautifulSoup(markup)

soup.get_text()
u'\nI linked to example.com\n'
soup.i.get_text()
u'example.com'
```

可以通过参数指定tag的文本内容的分隔符:

```
# soup.get_text("|")
u'\nI linked to |example.com|\n'
```

还可以去除获得文本内容的前后空白:

```
# soup.get_text("|", strip=True)
u'I linked to|example.com'
```

或者使用 [.stripped_strings](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#strings-stripped-strings) 生成器,获得文本列表后手动处理列表:

```
[text for text in soup.stripped_strings]
# [u'I linked to', u'example.com']
```



## 输出格式

Beautiful Soup输出是会将HTML中的特殊字符转换成Unicode,比如“&lquot;”:

```
soup = BeautifulSoup("&ldquo;Dammit!&rdquo; he said.")
unicode(soup) # Python 2 version
# u'<html><head></head><body>\u201cDammit!\u201d he said.</body></html>'
```

如果将文档转换成字符串,Unicode编码会被编码成UTF-8.这样就无法正确显示HTML特殊字符了:

```
str(soup)
# '<html><head></head><body>\xe2\x80\x9cDammit!\xe2\x80\x9d he said.</body></html>'

```





## 编码

使用Beautiful Soup解析后,文档都被转换成了Unicode:

```
markup = "<h1>Sacr\xc3\xa9 bleu!</h1>"
soup = BeautifulSoup(markup)
soup.h1
# <h1>Sacré bleu!</h1>
soup.h1.string
# u'Sacr\xe9 bleu!'
```

`BeautifulSoup` 对象的 `.original_encoding` 属性记录了自动识别编码的结果:

```
soup.original_encoding
'utf-8'
```

[编码自动检测](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#unicode-dammit) 功能大部分时候都能猜对编码格式,但有时候也会出错.有时候即使猜测正确,也是在逐个字节的遍历整个文档后才猜对的,这样很慢.如果预先知道文档编码,可以设置编码参数来减少自动检查编码出错的概率并且提高文档解析速度.在创建 `BeautifulSoup` 对象的时候设置 `from_encoding` 参数.

通过传入 `from_encoding` 参数来指定编码方式:

```
soup = BeautifulSoup(markup, from_encoding="iso-8859-8")
soup.h1
<h1>םולש</h1>
soup.original_encoding
'iso8859-8'
```



### 编码输出

通过Beautiful Soup输出文档时,不管输入文档是什么编码方式,输出编码均为UTF-8编码,

如果不想用UTF-8编码输出,可以将编码方式传入 `prettify()` 方法:

```
print(soup.prettify("latin-1"))
# <html>
#  <head>
#   <meta content="text/html; charset=latin-1" http-equiv="Content-type" />
# ...
```

还可以调用 `BeautifulSoup` 对象或任意节点的 `encode()` 方法,就像Python的字符串调用 `encode()` 方法一样:

```
soup.p.encode("latin-1")
# '<p>Sacr\xe9 bleu!</p>'

soup.p.encode("utf-8")
# '<p>Sacr\xc3\xa9 bleu!</p>'
```



### 检测编码

[编码自动检测](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#unicode-dammit) 功能可以在Beautiful Soup以外使用,检测某段未知编码时,可以使用这个方法:

```
from bs4 import UnicodeDammit
dammit = UnicodeDammit("Sacr\xc3\xa9 bleu!")
print(dammit.unicode_markup)
# Sacré bleu!
dammit.original_encoding
# 'utf-8'
```

如果Python中安装了 `chardet` 或 `cchardet` 那么编码检测功能的准确率将大大提高.输入的字符越多,检测结果越精确,如果事先猜测到一些可能编码,那么可以将猜测的编码作为参数,这样将优先检测这些编码:

```
dammit = UnicodeDammit("Sacr\xe9 bleu!", ["latin-1", "iso-8859-1"])
print(dammit.unicode_markup)
# Sacré bleu!
dammit.original_encoding
# 'latin-1'
```

# 解析部分文档

如果仅仅因为想要查找文档中的<a>标签而将整片文档进行解析,实在是浪费内存和时间.最快的方法是从一开始就把<a>标签以外的东西都忽略掉. `SoupStrainer` 类可以定义文档的某段内容,这样搜索文档时就不必先解析整篇文档,只会解析在 `SoupStrainer` 中定义过的文档. 创建一个 `SoupStrainer` 对象并作为 `parse_only` 参数给 `BeautifulSoup` 的构造方法即可.



## SoupStrainer

`SoupStrainer` 类接受与典型搜索方法相同的参数：[name](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id32) , [attrs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#css) , [recursive](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#recursive) , [text](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#text) , [**kwargs](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#keyword) 。下面举例说明三种 `SoupStrainer` 对象：

```
from bs4 import SoupStrainer

only_a_tags = SoupStrainer("a")

only_tags_with_id_link2 = SoupStrainer(id="link2")

def is_short_string(string):
    return len(string) < 10

only_short_strings = SoupStrainer(text=is_short_string)
```

再拿“爱丽丝”文档来举例，来看看使用三种 `SoupStrainer` 对象做参数会有什么不同:

```
html_doc = """
<html><head><title>The Dormouse's story</title></head>

<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""

print(BeautifulSoup(html_doc, "html.parser", parse_only=only_a_tags).prettify())
# <a class="sister" href="http://example.com/elsie" id="link1">
#  Elsie
# </a>
# <a class="sister" href="http://example.com/lacie" id="link2">
#  Lacie
# </a>
# <a class="sister" href="http://example.com/tillie" id="link3">
#  Tillie
# </a>

print(BeautifulSoup(html_doc, "html.parser", parse_only=only_tags_with_id_link2).prettify())
# <a class="sister" href="http://example.com/lacie" id="link2">
#  Lacie
# </a>

print(BeautifulSoup(html_doc, "html.parser", parse_only=only_short_strings).prettify())
# Elsie
# ,
# Lacie
# and
# Tillie
# ...
#
```

还可以将 `SoupStrainer` 作为参数传入 [搜索文档树](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id24) 中提到的方法.这可能不是个常用用法,所以还是提一下:

```
soup = BeautifulSoup(html_doc)
soup.find_all(only_short_strings)
# [u'\n\n', u'\n\n', u'Elsie', u',\n', u'Lacie', u' and\n', u'Tillie',
#  u'\n\n', u'...', u'\n']
```







# Example

## weather.com.cn

```
import requests
from bs4 import BeautifulSoup


def parse_page(url):
    headers = {
        "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
    }
    response = requests.get(url, headers=headers)
    text = response.content.decode('utf-8')

    soup = BeautifulSoup(text, 'html5lib')
    conMidtab = soup.find('div', class_="conMidtab")
    tables = conMidtab.find_all('table')
    for table in tables:
        trs = table.find_all('tr')[2:]
        for index, tr in enumerate(trs):
            tds = tr.find_all('td')
            city_td = tds[0]
            if index == 0:
                city_td = tds[1]
            city = list(city_td.stripped_strings)[0]
            high_temp_td = tds[-5]
            high_temp = list(high_temp_td.stripped_strings)[0]
            low_temp_td = tds[-2]
            low_temp = list(low_temp_td.stripped_strings)[0]
            print(city, high_temp, low_temp)


def main():
    urls = [
        'http://www.weather.com.cn/textFC/hb.shtml',
        'http://www.weather.com.cn/textFC/db.shtml',
        'http://www.weather.com.cn/textFC/hd.shtml',
        'http://www.weather.com.cn/textFC/hz.shtml',
        'http://www.weather.com.cn/textFC/hn.shtml',
        'http://www.weather.com.cn/textFC/xb.shtml',
        'http://www.weather.com.cn/textFC/xn.shtml',
        'http://www.weather.com.cn/textFC/gat.shtml'
        ]
    for url in urls:
        parse_page(url)

if __name__ == '__main__':
    main()
```



