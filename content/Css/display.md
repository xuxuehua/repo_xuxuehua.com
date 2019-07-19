---
title: "display 属性"
date: 2018-11-17 14:46
---


[TOC]

# display

用于控制layout的属性，每一个标签有一个默认的display value 作为控制，较多使用block 或者inline作为默认值



## 标签显示模式

### none

从文档流中移除

如script标签，用于隐藏和显示元素

和visibility不同的是，`display:none`会渲染页面， `visibility:hidden` 尽管隐藏了元素，但是始终会占用页面位置

```
p {
    display: none;
}
```



### block

按照块级元素显示，前后带换行符，自己独占一行。

如div, p, form标签，从左到右尽可能的占用空间.  在html5中，如header, footer, section



从内联元素变成块元素 ，可以设定行高

```
span1 {
    display: block;
}
```



### inline

按照内联元素显示，一个挨着一个, 不会破坏页面布局和

从块元素变成内联元素，不能设定行高

如span, a标签等，包裹住定义的文本

```
<span>my text</span>
```





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





