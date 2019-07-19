---
title: "overflow"
date: 2019-07-17 14:06
---
[TOC]



# overflow





父容器高度塌陷

如果一个标准流中的盒子中所有的子元素都进行了浮动，而且盒子没有设置高度，那么父容器整个高度会塌陷， 即图片超出了container的大小

给浮动元素的容器添加`overflow:hidden;`或`overflow:auto;`可以清除浮动，另外在 IE6 中还需要触发 hasLayout ，例如为父元素设置容器宽高或设置 zoom:1。在添加overflow属性后，浮动元素又回到了容器层，把容器高度撑起，达到了清理浮动的效果。

```
.parent {
    overflow: hidden;
}
```



## visible

内容不会被修剪，会呈现在元素框之外（默认操作）





## hidden

溢出内容会被修剪，并且被修剪的内容是不可见的



## auto

在需要时产生滚动条，即自适应所要显示的内容



## scroll

溢出内容会被修剪，并且浏览器会始终显示滚动条