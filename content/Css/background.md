---
title: "background"
date: 2019-03-07 19:08
---

[TOC]

# background

## background-origin 设置背景从什么区域显示

padding-box 背景图片相对于内边距 （默认值）

border-box 背景图片相对于边框定位 （以边框左上角为参照）

content-box背景图片相对于内容区域定位 （以内容区域坐上叫为参照）

## background-clip 设置背景在什么区域显示

border-box 背景被裁切到边框盒子位置 （背景图在整个容器中显示）

padding-box 背景被裁切到内边距区域（背景图片在内边距区域【包含内容区域】显示）

content-box 背景被裁切到内容区域（背景图在内容区域显示）

## background-size 背景图片尺寸

cover 

contain 

## background-attachment

背景图像是否固定或者随着页面的其余部分滚动

```
body {
    background-attachment: fixed;
}
```

scroll 默认值，背景图片随着滚动条滚动

fixed    背景图片不滚动，其余部分滚动

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


