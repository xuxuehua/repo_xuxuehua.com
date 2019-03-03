---
title: "tags 标签"
date: 2018-11-06 20:08
---


[TOC]

# 标签

## `<!DOCTYPE html> ` html5版本

标签位于文档的最前面，用于向浏览器说明当前文档使用哪种HTML或XHTML标准规范

主要用于浏览器解析文档标签的依据

其与浏览器兼容性有关，若删除，将由浏览器决定展示权利





## `<html>`

### `<html lang="en">`

lang='zh-CN' 

Lang 设定语言，帮助搜素引擎解析页面





## `<head>`

头部标签，用于封装其他标签内容



### `<title>`

设置浏览器的标题文字



### `<meta>`

```
<meta charset="UTF-8">
```

字符集设定，当前文档的编码格式是UTF-8





## `<body>`

包括实际内容

一个html中只能有一对body



### bgcolor

背景颜色

```
<body bgcolor="#000000">
</body>
```



## 呈现标签

### `<h1>~<h6>`标题



### `<p> `段落

用来定义一段文字



### `<!-- -->`注释





### `<br>`换行



### `<hr>`水平线



## 文本编辑标签



### `<b>` 粗体

定义粗体文本



### `<big>` 大号字





### `<span>`文本节

行内标签

内联元素，可以作为文本的容器，而且只是文本的容器





### `<em>`倾斜



### `<strong>`加粗



### `<sup>`上标

2的3次方 

`2<sup>3</sup>`



### `<sub>`下标

水分子

```
H<sub>2</sub>O
```



### `<ins>` 插入字



### `<del>`删除字





## `<div>` 块标签

网页块的包裹，与CSS 样式配合使用

块元素，用于组合HTML元素的容器





## `<a>`  超链接

```
<a href="http://www.xuxuehua.com">content</a>
```



### name

文档内的的链接，可以用作跳转

```
<a name="#h1"></a>
```



### href

指向另一个文档链接

```
<a href="http://xuxuehua.com" >hello</a>
```





### target 属性

`_blank` 在新页面

```
<a target="_blank" href="http://xuxuehua.com">xuxuehua</a>
```



`_self` 在当前页面打开

```
<a target="_self" href="http://xuxuehua.com">xuxuehua</a>
```



### 锚点链接

在当前页面滚动到指定位置

href 为标签的id

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>基本特点</h1>
<ul>
    <li>
        <a href="#js1">1.js</a>
    </li>
    <li>
        <a href="#js2">2.interactive</a>
    </li>
    <li>
        <a href="#js3">3.embeded</a>
    </li>
    <li>
        <a href="#js4">4.multiplatform</a>
    </li>
    <li>
        <a href="#js5">5.scriptlanguage</a>
    </li>
</ul>


<p id="js1">1.JavaScript是一种属于网络的脚本语言,已经被广泛用于Web应用开发,常用来为网页添加各式各样的动态功能,为用户提供更流畅美观的浏览效果。通常JavaScript脚本是通过嵌入在HTML中来实现自身的功能的。 [3]  <br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>  </p>
是一种解释性脚本语言（代码不进行预编译）。 [4] 
<p id="js2">2.主要用来向HTML（标准通用标记语言下的一个应用）页面添加交互行为。 [4] <br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br> </p>
<p id="js3">3.可以直接嵌入HTML页面，但写成单独的js文件有利于结构和行为的分离。 [4] <br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br> </p>
<p id="js4">4.跨平台特性，在绝大多数浏览器的支持下，可以在多种平台下运行（如Windows、Linux、Mac、Android、iOS等）。 <br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br> </p>
<p id="js5">5.Javascript脚本语言同其他语言一样，有它自身的基本数据类型，表达式和算术运算符及程序的基本程序框架。Javascript提供了四种基本的数据类型和两种特殊数据类型用来处理数据和文字。而变量提供存放信息的地方，表达式则可以完成较复杂的信息处理。 [5] </p>
</body>
</html>
```



## `<img>` 图片标记

```
<img src="" alt="">
```





### 属性

src 属性: 指定文件路径

alt属性: 指定不能显示图片时替换的文本

title: 鼠标悬停的时候显示的内容

width: 宽

height： 高

border：边框宽度

align left： 左对齐

align right： 右对齐

align top：顶端和文本的第一行文字对齐，其他文字居图像下方

align middle：水平重点和文本的第一行文字对齐，其他文字居图像下方

align bottom：底部和文本的第一行文字对齐，其他文字居图像下方







## `<form>` 表单

完整表单由表单控件，提示信息，表单域构成

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>form example</title>
</head>
<body>
    <form action="backend processing address" method="POST">
        <input type="text" name="text_name" value="123">
        <input type="button" value="add">
    </form>
</body>
</html>
```



### 属性

type: text 文本框

​	password 密码框

​	radio 单选

    <form action="">
        <input type="radio" name="r" id="r1">
        <label for="r1">r1</label>
        <input type="radio" name="r" id="r2">
        <label for="r2">r2</label>
    </form>
​	checkbox 复选 

```
    <form action="">
        <input type="checkbox" name="ckk_lq" value="1", id="ck_lq_id">
        <label for="ch_lq_id">myinfo1</label>
        <input type="checkbox" name="ckk_lq" id="ck_zq_id">
        <label for="ch_zq_id">myinfo2</label>
    </form>
```



​	submit

​	reset

​	image 图像式提交按钮

​	hidden 隐藏域

​	file 文件域



name： 控件名称

value： input控件中默认文本值

size： input控件在页面中显示宽度

readonly：只读，不能修改

disabled：显示控件为灰色

checked：默认被选中项

maxlength：控件允许的最多字符数



action:  路径， #表示当前页面



## `<textarea>` 文本控件

需要输入大量的信息是来处理



###  属性

rows -> rows="4"

cols -> cols="20"





## `<select>` 下拉菜单

```
    <select name="" id="">
        <option value="1">value1</option>
        <option value="2">value2</option>
        <option value="3">value3</option>
        <option value="4">value4</option>
    </select>
    <hr>
    <select multiple>
        <option value="1">value1</option>
        <option value="2">value2</option>
        <option value="3">value3</option>
        <option value="4">value4</option>
    </select>
```





## `<fieldset>` 组合表单

将表单内容的一部分打包，生成一组相关表单的字段

`<legend>` 标签为fieldset 元素定义标题

```
    <fieldset>
        <legend>my value</legend>
        <input type="radio" name="k" id="k1">
        <label for="k1">value k1</label>
        <input type="radio" name="k" id="k2">
        <label for="k2">value k1</label>
        <input type="radio" name="k" id="k3">
        <label for="k3">value k1</label>
    </fieldset>
```



## 表单练习

![img](https://cdn.pbrd.co/images/HMlCTG1.png)



```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>New contact</h1>

    <form action="#">
        <table border="1">
            <tr>
                <td>Name</td>
                <td><input type="text" name="text_name" id="text_id"></td>
            </tr>
            <tr>
                <td>Cellphone</td>
                <td><input type="text" name="phone_name" id="phone_id"></td>
            </tr>
            <tr>
                <td>cellphone type</td>
                <td>
                    <input type="radio" name="r_number" id="r_home">
                    <label for="r_home">home number</label>
                    <input type="radio" name="r_number" id="r_work">
                    <label for="r_work">work number</label>
                </td>
            </tr>
            <tr>
                <td>public info</td>
                <td>
                    <input type="checkbox" name="ck_private" id="ck_private">
                    <label for="ck_private">private info</label>
                    <input type="checkbox" name="ck_public" id="ck_public">
                    <label for="ck_public">public info</label>
                </td>
            </tr>
            <tr>
                <td>city</td>
                <td>
                    <select>
                        <option value="1">Beijing</option>
                        <option value="2">Shanghai</option>
                        <option value="3">Guangzhou</option>
                        <option value="4">Shenzhen</option>
                    </select>
                </td>
            </tr>
            <tr>
                <td>comment</td>
                <td>
                    <textarea name="text_remark" id="text_remark" cols="30" rows="10"></textarea>
                </td>
            </tr>
            <tr>
                <td colspan="2">
                    <input type="button" value="New">
                    <input type="reset" value="reset">
                </td>
                <td></td>
            </tr>
        </table>
    </form>

</body>
</html>
```



## `<iframe>` 内联框架 （不常用）

包含另一个页面的内容





## `<link>` 头部标签

属于头部标签，需要放入到head标签中



引入dns预解析 (网站优化的一种方式)

提前解析域名

```
<link rel="dns-prefetch" href="http://tce.taobao.com">
```



引入网站icon 图标

```
<link rel="shortcut icon" href="http://www.my.com/info.ico">
```



引入css样式

```
<link rel="stylesheet" href="css/bg.css">
```



## `<style>` 样式

外部样式标

```
rel="stylesheet"
```



引入文档的类型

```
type="text/css"
```



边距

```
margin-left
```



## `<meta>` 

页面关键词

```
<meta name="keywords" content="SegmentFault,思否,javascript,php,python,java,mysql,ios,android,vue.js,node.js,html,css,ruby,git,docker,前端"/>
```



页面描述

```
<meta name="description" content="SegmentFault 思否 为开发者提供问答、学习与交流编程知识的平台，创造属于开发者的时代！"/>
```



搜索方式

```
<meta name="robots" content="index,follow">
```



页面刷新和重定向

```
<meta http-equiv="refresh" content="1;url=">
```



优先使用chrome

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
```



禁用本地缓存

```
<meta http-equiv="Pragma" content="no-cache">
```





## 特殊标记

html为特殊符号准备了专门的替代码

如空格 `&nbsp`; 小于 `&lt` ； 大于 `&gt`;





## HTML 5新的语义标签

![img](https://cdn.pbrd.co/images/HMmFjz8.png)



常用 nav, header, footer, section, article, aside

```
 <body>
    <header>This is header</header>

    <article>
        <h1>H1 title</h1>
        <p>This is p info</p>
    </article>
    <aside>
        aside info
    </aside>

    <footer>
        <hr>
        This is footer info.
    </footer>
</body>
```



### 智能表单标签

![img](https://cdn.pbrd.co/images/HMmH0Z2.png)



* 隐藏域

```
<input type="hidden" name="" id="tmd" value="123">
```



### `<audio>` 音频

```
<audio controls>
<source src="./music/see_you_again.mp3" controls autoplay>
</audio>
```



### `<video>` 视频

支持h.264 编码， 音频支持ACC

```
<video src="./assert/seeyou.mp4" width=400 controls autoplay>
```



# 通用属性

id：唯一表识元素，在同一页面不能重复

name：用于定义一组标签，可以重复

style：定义标签的样式

class：定义标签的样式类



id，class，都是css选择器和js的dom都是开发的必备基础

img标签和a标签一般都会有title属性



## 行内元素

不占有独立的区域，仅仅靠自身的字体大小和图像尺寸来支持结构。

一般不可以设置宽度，高度，对齐等属性

strong, b, em, i, del, s, ins, u, a, span

span最典型



### 块元素

通常独自占据一整行或者多整行，可以对其设置高度，宽度，对齐等属性

h1~h6, p, div, ul, ol, li



# 不常用语义标签

## `<code>` 

标注计算机代码



## `<var>`

定义变量

## `<pre>`

定义预格式文本

## `<abbr>`

定义缩写

## `<address>`

定义地址

## `<blockquote>`

定义长引用

## `<cite>`

定义引用，引证

## `<dfn>`

定义一个定义项目

