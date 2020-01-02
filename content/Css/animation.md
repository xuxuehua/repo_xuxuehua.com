---
title: "animation"
date: 2019-03-16 20:06

---

[TOC]

# animation 动画

## 定义动画集

```
// 定义动画集
@keyframes anim_name {
    from {
        transform: rotateZ(0deg);
    }
    to {
        transform: rotateZ(360deg);
    }    
}


//或者
@keyframes anim_name {
    0%{

    }
    100{

    }
}
```

## 使用动画集

```
.box {
width: 0;
height: 0;
border-left: 100px dashed red;
border-top: 100px dashed green;
border-right: 100px dashed blue;
border-bottom: 100px dashed yellow;

animation-name: anim_name;
animation-duration: 2s;
animation-iteration-count: 1; //执行次数
animation-time-function: linear; // 速度为匀速
animation-direction: alternate; // 动画逆播
animation-delay: 1s; 
animation-fill-mode: forwards; //动画结束了，但是不能是无限次
}


.box:hover{
    animation-play-state: paused; //鼠标进入就停止
}
```

```
div{
    width: 100px;
    height: 100px;
    background-color: blue;
    position: relative;
    animation: anim 5s infinite alternate;
    -webkit-animation: anim 5s infinite alternate;  
}

@keyframes anim{
    0%{background:red; left:0px; top:0px}
    25%{background:blue; left:200px; top:0px}
    50%{background: #ccffcc; left: 200px; top: 200px}
    75%{background: #00ffff; left: 0px; top: 200px}
    100%{background: red; left: 0px; top: 0px}
}
@-webkit-keyframes anim{
    0%{background:red; left:0px; top:0px}
    25%{background:blue; left:200px; top:0px}
    50%{background: #ccffcc; left: 200px; top: 200px}
    75%{background: #00ffff; left: 0px; top: 200px}
    100%{background: red; left: 0px; top: 0px}
}
```

### 魔法效果

```html

```

# transition 过渡属性

```
.box{
    transition-property: all;
    transition-delay: 1;
    transition-duration: 2s;
    transition-time-function: linear;
}
```

```
div{
    width: 100px;
    height: 100px;
    background-color: blue;
    -webkit-transition: width 2s,height 2s,-webkit-transform 2s;
    transition:width 2s, height 2s, transform 2s;    
}

div:hover{
    width:200px;
    height:200px;
    transform:rotate(360deg);
    -webkit-transform:rotate;    
}
```

#### transition-property 过渡名称

all代表所有的属性

写的属性就是对应属性的过渡效果

```
transition-property: width;
```

#### transition-duration 效果时间

#### transition-timing-function 效果的时间曲线

#### transition-dely 延时开始时间

# transform

## transform-style 效果

```
transform-style: preserve-3d;
```

> 3d 效果

## translate 移动 2d

```
.div2 {
    transform: translate(100px,100px);
    -webkit-transform: translate(100px,100px);
}
```

>  -webkit-transform 是chrome safari支持的方式
> -ms-transform 是IE
> 
> -o-transform 是Opera
> 
> -moz-transform 是Firefox

```
transform: translate(100px);
```

> 横坐标相对于原来位置发生偏移

```
transform: translate(100px, 100px);
```

> 横纵坐标相对于原来位置发生偏移

### translateX 沿x轴位移

定义转换，只是用 X 轴的值

### translateY 沿y轴位移

定义转换，只是用 Y 轴的值。

### translateZ 沿z轴位移

定义 3D 转换，只是用 Z 轴的值。

## rotate 旋转

```
.div2{
    transform: rotate(180deg);
    -webkit-transform: rotate(180deg);
}
```

```
transform: rotate(30deg,40deg);
```

> 沿x轴的方向旋转30，沿y轴的方向旋转30

### rotateX

```
.div2{
    transform: rotateX(120deg);
    -webkit-transform: rotateX(120deg);
}
```

### rotateY

```
.div2{
    transform: rotateY(120deg);
    -webkit-transform: rotateY(120deg);
}
```

### rotateZ 沿z轴旋转

## scale 缩小放大

```
.div2{
    transform: scale(1,2); 
    -webkit-transform: scale(1,2);
}
```

> 参数1为宽度
> 
> 参数2位倍数

```
transform: scale(0.2，2);
```

> 宽度为原来的0.2， 高度为原来的2倍

### scaleX

### scaleY

### scaleZ

## skew

```
.div2{
    transform: skew(20deg,20deg); 
    -webkit-transform: skew(20deg,20deg);
}
```

> 参数1为X轴
> 
> 参数2位Y轴

### skewX 沿x轴倾斜

### skewY 沿y轴倾斜

# # example

## Back to top

```
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
}

#myBtn {
  display: none;
  position: fixed;
  bottom: 20px;
  right: 30px;
  z-index: 99;
  font-size: 18px;
  border: none;
  outline: none;
  background-color: red;
  color: white;
  cursor: pointer;
  padding: 15px;
  border-radius: 4px;
}

#myBtn:hover {
  background-color: #555;
}
</style>
</head>
<body>

<button onclick="topFunction()" id="myBtn" title="Go to top">Top</button>

<div style="background-color:black;color:white;padding:30px">Scroll Down</div>
<div style="background-color:lightgrey;padding:30px 30px 2500px">This example demonstrates how to create a "scroll to top" button that becomes visible 
  <strong>when the user starts to scroll the page</strong>.</div>

<script>
//Get the button
var mybutton = document.getElementById("myBtn");

// When the user scrolls down 20px from the top of the document, show the button
window.onscroll = function() {scrollFunction()};

function scrollFunction() {
  if (document.body.scrollTop > 20 || document.documentElement.scrollTop > 20) {
    mybutton.style.display = "block";
  } else {
    mybutton.style.display = "none";
  }
}

// When the user clicks on the button, scroll to the top of the document
function topFunction() {
  //document.body.scrollTop = 0;
   $(&#39;html,body&#39;).animate({ scrollTop: 0 }, &#39;slow&#39;); // 慢速滚动
  document.documentElement.scrollTop = 0;
}
</script>

</body>
</html>
```
