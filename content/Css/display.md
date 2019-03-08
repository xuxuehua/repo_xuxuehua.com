---
title: "display"
date: 2018-11-17 14:46
---


[TOC]


# display



## 标签显示模式

### none

从文档流中移除

```
p {
    display: none;
}
```



### block

按照块级元素显示，前后带换行符，自己独占一行。

从内联元素变成块元素 ，可以设定行高

```
span1 {
    display: block;
}
```



### inline

按照内联元素显示，一个挨着一个

从块元素变成内联元素，不能设定行高



### inline-block

按行内标签进行排版，但是可以设置 宽高，如图片

```
span1 {
    background-color: red;
    height: 400px;
    weight: 400px;
    display: inline-block;
}
```



## 颜色表示

## RGB 

```
rgb(red, gree, blue)
```

> 每个参数定义颜色强度，0-255之间，或者0%-100%

```
p {color: rgb(255,255,0);};
p {color: rgb(100%,100%,0);};
```



### 十六进制

rgb的另一种写法

`#RRGGBB`

```
#000000 - #FFFFFF 之间
```





