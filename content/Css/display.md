---
title: "display 属性"
date: 2018-11-17 14:46
---

[TOC]

# display

用于控制layout的属性，每一个标签有一个默认的display value 作为控制，较多使用block 或者inline作为默认值

## none

从文档流中移除

如script标签，用于隐藏和显示元素

和visibility不同的是，`display:none`会渲染页面， `visibility:hidden` 尽管隐藏了元素，但是始终会占用页面位置

```
p {
    display: none;
}
```

## block

按照块级元素显示，前后带换行符，自己独占一行。

如div, p, form标签，从左到右尽可能的占用空间.  在html5中，如header, footer, section

从内联元素变成块元素 ，可以设定行高

```
span1 {
    display: block;
}
```

## inline

按照内联元素显示，一个挨着一个, 不会破坏页面布局和

从块元素变成内联元素，不能设定行高

如span, a标签等，包裹住定义的文本

```
<span>my text</span>
```

## inline-block 行内排版

按行内标签进行排版，但是可以设置 宽高，如图片

```
span1 {
    background-color: red;
    height: 400px;
    weight: 400px;
    display: inline-block;
}
```

## flex 伸缩效果

```
display: flex;
```

### justify-content 排布方式

```
justify-content: flex-start; // 从左向右 默认
justify-content: flex-end; // 从右向左
justify-content: center； //中间显示 
justify-content: space-between; // 两端对齐方式
justify-content: space-around; // 两边空白 环绕对其
```

### flex-direction 主轴调整

```
flex-direction: row; // 主轴水平
flex-direction: row-reverse; // 主轴水平反转
flex-direction: column; // 主轴水平方向变成了垂直方向，侧轴永远都是垂直主轴的
flex-direction: column-reverse; // 主轴竖着反转
```

### flex-wrap 子元素换行

默认子元素没有换行

```
flex-wrap: wrap; // 子元素换行
flex-wrap: nowrap; // 默认值
flex-wrap: wrap-reverse; // 反转+换行
```

## align-items 侧轴对齐

```
align-items: flex-start; // 侧轴开始
align-items: flex-end; // 侧轴开始
align-items: center;
align-items: stretch; // 拉伸效果，但是需要弄掉子元素的高度
```

## align-content

```
align-content: flex-start; //子元素换行后有空白行
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



## 伪元素

伪元素是一个附加至选择器末的关键词，允许你对被选择元素的特定部分修改样式。伪元素主要有：

```
::first-letter 第一个字母的样式
::first-line   首行文字的样式
::before  元素头部添加的修饰
::after   元素尾部添加的修饰
::placeholder input的占位符样式
::selection 被选中元素的样式
```

​    伪元素可以解释为元素的修饰，可以为元素带来额外的附加样式，属于额外的文档结构。



## `:before` `:after`

伪类的样式渲染

`:before` and `:after` create pseudo elements as 'children' for the element they are applied to. They are often used for certain stylings, or error messages.

```
<p>some text</p>
```



```
p:after {
    content: 'hi! im after';
    color: red;
    padding: 0 10px;    
}

p:before {
    content: 'hi! im before';
    color: blue;
    padding: 0 10px;
}

p {
    color: orange;
}
```



![image-20200112214216405](image-20200112214216405.png)



## 伪类

用来表示无法在CSS中轻松或者可靠检测到的某个元素的状态或属性，比如a标签的hover表示鼠标经过的样式，visited表示访问过的链接的样式，更多的用来描述元素状态变化时的样式，伪类主要有：

```
:link
:visited
:hover
:active
:focus
:lang(fr)
:not(s)
:root
:first-child
:last-child
:only-child
:nth-child(n)
:nth-last-child(n)
:first-of-type
:last-of-type
:only-of-type
:nth-of-type(n)
:nth-last-of-type(n)
:empty
:checked
:enabled
:disabled
:target
```

​    



### Hover 案例

```
div {
    width: 300px;
    height: 50px;
    background: yellow
}

div:before {
    content: "Before";
    background: red;
}

div:hover:after {
    content: "Hovered After";
    background: blue;
    color: white;
}
```

http://jsfiddle.net/XjUM8/2/



### hover 提示弹窗

```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <style>
        * {
            margin: 0px;
            padding: 0px;
        }

        body {
            text-align: center;
        }

        p {
            margin: 100px;
        }

        .tip {
            display: inline-block;
            position: relative;
        }

        .tip:before, .tip:after {
            opacity: 0; /*透明度为完全透明*/
            position: absolute;
            z-index: 1000; /*设为最上层*/
            /*鼠标放上元素上时的动画，鼠标放上后效果在.tip-*:hover:before, .tip-*:hover:after中设置;
            0.3s:规定完成过渡效果需要多少秒或毫秒,ease:规定慢速开始，然后变快，然后慢速结束的过渡效果*/
            transition: 0.3s ease;
            -webkit-transition: 0.3s ease;
            -moz-transition: 0.3s ease;
        }

        .tip:before {
            content: '';
            border: 6px solid transparent;
        }

        .tip:after {
            content: attr(data-tip); /*后去要提示的文本*/
            padding: 5px;
            white-space: nowrap; /*强制不换行*/
            background-color: #000000;
            color: #ffffff;
        }

        .tip:hover:before, .tip:hover:after {
            opacity: 1; /*鼠标放上时透明度为完全显示*/
            z-index: 1000;
        }

        /*top*/
        .tip-top:before {
            bottom: 100%;
            left: 50%;
            border-top-color: rgba(0, 0, 0, 0.8); /*小三角效果*/
            margin-left: -3px;
            margin-bottom: -12px;
        }

        .tip-top:after {
            bottom: 100%;
            left: 50%;
            margin-left: -6px;
        }

        .tip-top:hover:before {
            margin-bottom: -6px;
        }

        .tip-top:hover:after {
            margin-bottom: 6px;
        }

        /*bottom*/
        .tip-bottom:before {
            top: 100%;
            left: 50%;
            border-bottom-color: rgba(0, 0, 0, 0.8);
            margin-left: -3px;
            margin-top: -9px;
        }

        .tip-bottom:after {
            top: 100%;
            left: 50%;
            margin-left: -6px;
            margin-top: 3px;
        }

        .tip-bottom:hover:before {
            margin-top: -3px;
        }

        .tip-bottom:hover:after {
            margin-top: 9px;
        }

        /*right*/
        .tip-right:before {
            top: 50%;
            left: 100%;
            border-right-color: rgba(0, 0, 0, 0.8);
            margin-left: -9px;
            margin-top: -3px;
        }

        .tip-right:after {
            top: 50%;
            left: 100%;
            margin-left: 3px;
            margin-top: -6px;
        }

        .tip-right:hover:before {
            margin-left: -3px;
        }

        .tip-right:hover:after {
            margin-left: 9px;
        }

        /*left*/
        .tip-left:before {
            top: 50%;
            left: 0%;
            border-left-color: rgba(0, 0, 0, 0.8);
            margin-left: 0px;
            margin-top: -3px;
        }

        .tip-left:after {
            top: 50%;
            right: 100%;
            margin-right: 0px;
            margin-top: -6px;
        }

        .tip-left:hover:before {
            margin-left: -6px;
        }

        .tip-left:hover:after {
            margin-right: 6px;
        }
    </style>
</head>
<body>
<p>
    <button class="tip tip-top" data-tip="我是上边提示">上边提示</button>
</p>
<p>
    <button class="tip tip-bottom" data-tip="我是下边提示">下边提示</button>
</p>
<p>
    <button class="tip tip-right" data-tip="我是右边提示">右边提示</button>
</p>
<p>
    <button class="tip tip-left" data-tip="我是左边提示">左边提示</button>
</p>
</body>
</html>
```





