---
title: "selector 选择器"
date: 2018-11-13 14:31
---


[TOC]

# selector 选择器



## 选择器优先级

ID > 类（伪类）> 标签 > 继承 > 默认



## 通配符选择器 （不常用）

穿透力很强，优先级高于继承的样式，会覆盖继承的样式

```
* {}
```

```
* {
    margin: 0; /* 定义外边距 */
    padding: 0; /* 定义内边距 */
}
```



## 标签选择器

```
p {}
div {}
```

选择所有的p标签都设置成字体为红色

```
p {color: red;}
```



## ID 选择器

id命名必须以字符开头，包含数字，字母，下划线，连接符

```
#head {}
```



相同标签的不同展现形式

```css
<li id="a">Beijing</li>
<li id="b">Shanghai</li>
<li id="c">Guangzhou</li>
>>>

<style>
#a {
    background-color: red;
}
#b {
    background-color: blue;
}
#c {
    background-color: yellow;
}
</style>
```



## 类选择器

对class 属性进行选择

```
.head {}
```

```
.demo {
    color: red;
}
```



标签可以包含多个类选择器，在class标签中用空格隔开



## 复合选择器

### 标签指定式选择器 （使用较少）

两个或者多个基础选择器通过不同方式组合而成

第一个为标记选择器，第二个为class选择器或者id选择器，选择器之间不能有空格

```
h3.class {
    background-color: blue;
}

p#one {
    background-color: yellow;
}
```



### 后代选择器（包含选择器）（常用）

用来选择元素或者元素组的后代，把外层标记写在前面，内容标记写在后面，中间用空格分隔

```
.class h3 {
    color: red;
}
```



### 并集选择器 （常用 ）

各个选择器通过逗号连接而成，任何形式的的选择器都可以作为并集选择器的一部分

```
.f-news a, .s-news a {
    color: silver;
}
```



#### 常用的初始化设置

```
html, body, div, dt, dl, dd, ul, p {
    padding:0; /* 内边距 */
    margin:0; /* 外边距 */
}
```



### 子元素选择器

让CSS选择器智能选择子代的元素

```
<p>
	<span>test</span>
	<span><strong>no change</strong></span>
	<span>test</span>
	<strong>will change to red</strong>
</p>

p > strong {
    color: red;
}
```



## 层级选择器



## 分组选择器



## 属性选择器

属性和属性值必须完全匹配



![img](https://snag.gy/PCwbiW.jpg)



```
[title] {
    color: aqua;
}
```



```
[href] {
    font-size: 30px;
}
```







## 相邻兄弟选择器

可选择紧接在另一个元素后的元素，且二者有相同的父元素

```
<ul>
	<li>item1</li>
	<li>item2</li>
	<li>item3</li>
</ul>

li+li {
    font-size: 30px;
}
```

> item2和item3会变大，后的概念



## 伪类选择器



`:link`

伪类讲应用于未被访问过的链接，解决此问题，请使用a标签

`:hover`

伪类讲应用于鼠标指针悬停于其上的元素

`:active`

伪类讲应用于被激活的元素，如被点击的链接，被按下的按钮，但是鼠标不放开的时候

`:visited`

伪类将应用于已经被访问过的链接

`:focus`

伪类将应用于拥有键盘输入焦点的元素， 获取到输入的焦点的时候	



顺序遵循LVHA 原则

```
    <style>
        a {
            font-size: 300px;
        }
        a:link {
            color: blue;
        }
        a:visited {
            color: lawngreen;
        }
        a:hover {
            color: darkgreen;
            background-color: yellow;
        }
        a:active {
            color: gold;
        }
        input:focus {
            color: red;
        }
    </style>

</head>
<body>
    <a href="#">test 1</a>
    <a href="#">test 2</a>
    <a href="/">test 3</a>
    <input type="text" name="" id="">
</body>
```



## 伪元素选择器

`:first-child` 伪类，选择属于第一个子元素的元素

> `span:first-child{}` /* 选择属于第一个子元素的所有span标签 */

```
    <style>
        span:first-child{
            font-size: 50px;
        }
    </style>
</head>
<body>
    <div>
        <span>this is 1</span>
        <span>this is 2</span>
        <span>this is 3</span>
    </div>
    <p>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </p>
```

> this is 1 和1会改变





伪元素是控制内容V

`:first-line` 伪元素 /* 文本的第一行 */
`:first-letter` 伪元素 /* 文本的第一个字母 */
上述只能用于块级元素





指定标签的的前面和后面操作

```
::before 
::after
```

```
        p::before {
            content: "---";
        }
        p::after {
            content: "===";
        }
```

