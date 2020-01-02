---
title: "layout 布局"
date: 2018-11-20 18:06
---

[TOC]

# layout

## 盒模型

即把HTML页面中的元素看作是一个矩形的盒子容器，每个矩形都是有元素的内容（content），内边距（padding），边框（border）和外边距（margin） 组成

![img](https://snag.gy/sfBe6c.jpg)

> height 和weight 只对content区域有效果

### 清除样式（常用操作）

网站一般会将内边距盒外边距设置为0， 清除元素的默认边距设定

```
body,button,code,dd,details,dl,dt,fieldset,figcaption,figure,footer,form,h1,h2,h3,h4,h5,h6,header,hgroup,hr,input,legend,li,menu,nav,ol,p,pre,section,td,textarea,th,ul{margin:0;padding:0}
```

行内元素不要给上下的margin和padding，默认是不起作用的

```
body, div {
  margin: 0;
  padding: 0;
}
```

### border 边框

推荐方式

```
border: 20px solid purple;
```

border-style

```
none，没有边框，即忽略所有边框的宽度
solid，边框为单实线
dashed，边框为虚线
dotted，边框为点线
double，边框为双实线
```

border-color

```
border-color: red; 
border-color: yellow black green red;
```

> 多个参数顺序，为顺时针，上右下左

border-radius

圆角边框

box-shadow

边框阴影

border-image

边框图片

### padding 内边距

```
padding: 10px 20px 30px 40px
padding: 0;
```

> 多个参数顺序，为顺时针，上右下左 

### margin 外边距

默认为透明区域

接受任何长度单位，百分比数值

```
margin: 0 auto;
```

> 确保元素内容包裹在container之中

使用margin定义块元素的垂直外边距时，可能会出现外边距合并的现象

```
.container1 {
    margin: 100px;
}

.container2 {
    margin: 100px;
}
```

> 两个container之间的间距是100，不是200
> 
> 若不一样，以边距大的数值为最终结果

#### max-width

使用max-width替换掉width，会帮助浏览器处理小窗口，常用于mobile 页面

### box-sizing

设置`box-sizing: border-box`  会将padding和border元素相内增长

也可以设置在真个页面之上

```
* {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}
```

### 水平垂直居中

```
{
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -50%width; 
    margin-top: -50%height;
}
```

### 页面自适应

```
width: 100%
```

### 上下为0， 左右居中

```
margin: 0 auto;
```

## 流式布局

### 标准流

  把浏览器当成一个盒子容器，容器中排放盒子的时候，从上往下，从左向右排放

若放不下盒子，换行存放

### 浮动流

浮动的盒子可以向左或向右移动 

#### 浮动定义 (解决div)

```
div {
    float: right;
}
```

## inline-block

可设置每一列的宽度

受控于vertical-align 属性， 一般设置为top

[http://learnlayout.com/inline-block-layout.html](http://learnlayout.com/inline-block-layout.html

## 常用布局案例

![img](https://snag.gy/od7icm.jpg)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body, div, p {
            padding: 0;
            margin: 0;
        }

        div {
            border: 1px solid lawngreen;
        }

        .top-nav {
            height: 100px;
            width: 960px;
            margin: 0 auto;
        }

        .leftbar {
            float: left;
            width: 200px;
            height: 300px;
        }

        .content {
            float: left;
            width: 756px;
            height: 500px;
        }

        .main {
            width: 960px;
            margin: 0 auto;
            overflow: hidden;
        }

        .footer {
            margin: 0 auto;
            width: 960px;
        }


    </style>
</head>
<body>
    <div class="top-nav">
        top area
    </div>

    <div class="main">
    <div class="leftbar">
        left area
    </div>
    <div class="content">
        main area
    </div>
    </div>

    <div class="footer">
        footer area
    </div>
</body>
</html>
```

![img](https://snag.gy/kRrgib.jpg)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body, div {
            margin: 0;
            padding: 0;
        }

        div {
            border: 1px solid saddlebrown;
        }

        .head {
            height: 100px;
            background-color: yellow;
        }

        .left {
            float: left;
            width: 200px;
            background-color: lightblue;
            height: 400px;
        }

        .right {
            margin-left: 203px;
            height: 400px;
            background-color: red;
        }

        .footer {
            background-color: pink;
        }

    </style>
</head>
<body>
    <div class="head">
        head area
    </div>
    <div class="left">
        left area
    </div>
    <div class="right">
        right area
    </div>
    <div class="footer">
        footer area
    </div>
    <div>

    </div>

</body>
</html>
```

![img](https://snag.gy/K9QTVd.jpg)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body,div {
            padding: 0;
            margin: 0;
        }

        div {
            border: 1px solid saddlebrown;
        }

        .head {
            height: 100px;
            background-color: lightgreen;
        }

        .left {
            float: left;
            width: 100px;
            height: 300px;
        }

        .right {
            float: right;
            width: 100px;
            height: 500px
        }

        .main {
            margin: 0 105px;
        }

        .footer {
            clear: both;
        }
    </style>
</head>
<body>
    <div class="head">head area</div>    
    <div class="left">left area</div>    
    <div class="right">right area</div>    
    <div class="main">main area</div>    
    <div class="footer">footer area</div>    
</body>
</html>
```

![img](https://snag.gy/nagURi.jpg)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body,div {
            padding: 0;
            margin: 0;
        }

        div {
            border: 1px solid saddlebrown;
        }

        .head,.footer,.center {
            margin: 0 auto;
            width: 960;
        }

        .left,.right,.main {
            float: left;
        }

        .center {
            overflow: hidden;
        }

        .left , .right{
            width: 100px;
            height: 400px;
        }

        .main {
            width: 960px;
            margin: 0 2px;
        }
    </style>
</head>
<body>
    <div class="head">head area</div>    
    <div class="center">
    <div class="left">left area</div>    
    <div class="main">main area</div>    
    <div class="right">right area</div>    
    </div>
    <div class="footer">footer area</div>    
</body>
</html>
```

### 百分比width 布局

根据浏览器大小自适应

[http://learnlayout.com/media-queries.html](http://learnlayout.com/media-queries.html)

### 瀑布流

```
<body>
    <div class="container">
        <div>
            <img src="1.jpg">
        </div>
                <div>
            <img src="2.jpg">
        </div>
                <div>
            <img src="3.jpg">
        </div>
                <div>
            <img src="4.jpg">
        </div>
    </div>
</body>
```

```
.container{
    column-width: 250px;
    -webkit-column-width: 250px;
    -webkit-column-gap: 5px;
    column-gap: 5px;
}

.container div{
    width: 250px;
    margin: 5px 0;
}
```

### 三联布局

![img](https://snag.gy/yxZTbg.jpg)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        body {
            min-width: 700px;
        }
        .header, .footer {
            float: left;
            width: 100%;
            background-color: red;
            height: 40px;
            line-height: 40px;
            text-align: center;
        }
        .container {
            padding: 0 220px 0 220px;
        }
        .left, .right, .middle {
            position: relative;
            float: left;
            min-height: 200px;
        }
        .middle {
            width: 100%;
            background-color: orange;
        }
        .left {
            width: 220px;
            background-color: blue;
            margin-left: -100%;
            left: -220px;
        }
        .right {
            width: 220px;
            background-color: yellow;
            margin-left: -220px;
            right: -220px;
        }
    </style>
</head>
<body>
    <div class="header">
        <h4>header area</h4>
    </div>

    <div class="container">
        <div class="middle">
            <h4>middle area</h4>
        </div>
        <div class="left">
            <h4>left area</h4>
        </div>
        <div class="right">
            <h4>right area</h4>
        </div>
    </div>
    <div class="footer">
       <h4>footer area</h4> 
    </div>

</body>
</html>
```

## z-index 元素叠加

可以设置元素叠加顺序， 但依赖定位属性

z-index大的元素会覆盖小的元素

z-index为auto 不参与层级比较

z-index为负值，元素被普通流中的元素覆盖
