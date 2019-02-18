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





