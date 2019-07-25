---
title: "background"
date: 2019-03-07 19:08
---


[TOC]



# background



## background-attachment

背景图像是否固定或者随着页面的其余部分滚动

```
body {
    background-attachment: fixed;
}
```



scroll 默认值，背景图片随着滚动条滚动

fixed	背景图片不滚动，其余部分滚动





## background-color

设置元素的背景颜色

```
body {
    background-color: darkgray;
}
```



## background-image

把图片设置为背景

```
body {
    background-image: url("bg.jpg");
}
```



## background-position

设置背景图片的起始位置

```
长度值 (x, y) ， 百分比 (x%, y%)

top, right, left, bottom, center
```



```
body {
    background-position: right;
}
```

```
body {
    background-position: 20%, 20%;
}
```









## background-repeat

设置背景图片是否重复

```
body {
    background-repeat: no-repeat;
}
```

