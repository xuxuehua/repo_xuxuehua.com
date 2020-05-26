---
title: "position 属性"
date: 2019-07-17 10:24

---

[TOC]

# position

位置属性

[http://learnlayout.com/position-example.html](http://learnlayout.com/position-example.html)

## static

默认值， 遵循正常的文档流对象。

静态定位的元素不会受到 top, bottom, left, right影响。



## relative

类似于static， 默认位置没有被固定, 元素的定位是相对其原来位置进行偏移, 即依据父类元素的定位

relative 会影响到其它的元素  移动的元素 只是视觉上移动 还占着自身的位置 其它元素不能占据它的位置

可以设置top，right，bottom，left等属性来设定位置

```
.relative2 {
  position: relative;
  top: -20px;
  left: 20px;
  background-color: white;
  width: 500px;
}
```

## fixed

元素的位置相对于浏览器窗口是固定位置。

即使窗口是滚动的它也不会移动

可以设置top，right，bottom，left等属性来设定位置

```
.fixed {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 200px;
  background-color: white;
}
```

## absolute

绝对位置， 类似于fixed，元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`

即不影响周围元素，周围元素也不影响自己

一个元素可以放在页面上的任何一个位置

```
.absolute {
  position: absolute;
  top: 120px;
  right: 0;
  width: 300px;
  height: 200px;
}
```
