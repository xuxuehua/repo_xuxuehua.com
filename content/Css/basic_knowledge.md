---
title: "basic_knowledge"
date: 2018-11-11 13:35
---


[TOC]


# basic_knowledge



## 定义

层叠样式表(英文全称：Cascading Style Sheets)

用于设置HTML页面中的文字内容（字体，大小，对齐方式），图片外形（宽高，边框样式）及页面布局等外观显示样式



## 语法

```
p {color:red;}
```

> 选择符号p， 大小敏感
>
> 属性声明color， 大小不敏感，一般为小写
>
> 换行和空格不敏感



属性大于1个，使用分号隔开

```
h1 {color:red; font-size: 14px}
```



大于一个单词，加上引号

```
p {font-family: "sans serif"}
```



## 注释

```
/* .... */ 

```



## 样式

优先级自上往下

### Important 样式 （优先级最高）（少用）

```
p {
    color; red !important;
}
```



### 内联样式 （优先级其次高）

```
<div style="color: red; font-size:24px;border: 1px solid black;">
```





### 嵌入样式/页内样式 （优先级再其次）

* 和外部样式重复时，依据就近原则

* `<style>`

```
<style>
	p {
        color: yellowgreen;
	}
</style>
```





###  外部样式 （优先级再其次）

* 和嵌入样式重复时，依据就近原则

* `<link>`

index.css

```
p {
    /* define font */
    font-size: 50px;
    /* background color */
    background-color: silver;
}
```

Index.html 引入

```
<link rel="stylesheet" href="./css/index.css">
```





### 继承样式 （优先级再其次）

继承得到的样式

a标签除外



### 默认样式 （优先级最低）

html默认的样式

a标签除外





## 特性

### 层叠性

多层样式以最后一层样式为准

```
p {
    color:red;
}
p {
    color:green;
}
```

> 以green 为准



### 继承性

父容器设置的样式，子容器也可以享受相同的待遇

对于字体，文本属性等网页中通用的样式可以统一继承，如字体，字号，颜色，行距等

```
<style>
    p {
        color: red
    }
</style>
</head>
<body>
    <p>
        this is p
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </p>
```



a 标签默认不继承字体颜色

```
<style>
    p {
        color: red
    }
    a {
        color: inherit;
    }
</style>
</head>
<body>
    <p>
        this is p
        <a href="span">this is a</a>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </p>
```



## 简单属性

```
width： 宽度 （限制块结构）
height：高度 （限制块结构）
color：前景色，文字颜色
background-color： 背景色
font-size：字体的大小
```

# 响应式布局



## 支持移动端

可视区域为屏幕大小

```
<meta name="viewpoint" content="width=device-width, initial-scale=1.0">
```





# px 

PX特点

1. IE无法调整那些使用px作为单位的字体大小；

2. 国外的大部分网站能够调整的原因在于其使用了em或rem作为字体单位；

3. Firefox能够调整px和em，rem，但是96%以上的中国网民使用IE浏览器(或内核)。



任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。那么12px=0.75em,10px=0.625em。

为了简化font-size的换算，需要在css中的body选择器中声明Font-size=62.5%，这就使em值变为 16px*62.5%=10px, 这样12px=1.2em, 10px=1em, 也就是说只需要将你的原来的px数值除以10，然后换上em作为单位就行了。





# em

EM特点 

1. em的值并不是固定的；

2. em会继承父级元素的字体大小。



所以我们在写CSS的时候，需要注意两点：

1. body选择器中声明Font-size=62.5%；

2. 将你的原来的px数值除以10，然后换上em作为单位；

3. 重新计算那些被放大的字体的em数值。避免字体大小的重复声明



# rem

rem是CSS3新增的一个相对单位（root em，根em）

使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。



目前，除了IE8及更早版本外，所有浏览器均已支持rem。对于不支持它的浏览器，应对方法也很简单，就是多写一个绝对单位的声明。这些浏览器会忽略用rem设定的字体大小。



**看下面这个例子：**

```
p {font-size:14px; font-size:.875rem;}
```



# 转换工具

http://pxtoem.com/