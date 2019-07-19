---
title: "clear"
date: 2019-07-17 13:56
---
[TOC]



# clear

用于控制float属性

在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动



clear 可以用作清除浮动

优点：简单，代码少，浏览器兼容性好
缺点：需要添加大量无语义的html元素，代码不够优雅，后期不容易维护

```
clear:left/right/both;
```





# clearfix  闭合浮动

```
.clearfix {
  overflow: auto;
}
```



## BFC 

BFC特性使得标签的所有子容器都和依赖父容器， 即子容器的布局不会影响父容器的布局，外边距合并失效

触发BFC通过绝对定位和相对定位，float， display: table, overflow: hidden|auto （只要不是visible）



![img](https://snag.gy/KCSZP7.jpg)



```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>clear_float</title>

    <style>
        body, div {
            margin: 0;
            padding: 0;
        }
        div {
            border: 1px solid red;
        }
        .fl {
            float: right;
            height: 100px;
            width: 100px;
            background-color: #ccddaa;
        }
        .clearfix::after, .clearfix::before {
            content: " "; /* opera bug*/
            display: table;
        }
        .clearfix::after {
            clear: both;
        }
        .clearfix {
            *zoom: 1;  /* 兼容IE 6， 7 */
        }

    </style>
</head>
<body>
    <div class="clearfix">
        <div class="fl">浮动的div</div>
        段落内容
        段落内容
        段落内容
        段落内容
        段落内容
        段落内容
    </div>
</body>
</html>
```





