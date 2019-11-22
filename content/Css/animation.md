---
title: "animation"
date: 2019-03-16 20:06
---


[TOC]



# 动画



## 2D

### translate() 移动

```
.div2 {
    transform: translate(100px,100px);
    -webkit-transform: translate(100px,100px);
}
```



-webkit-transform 是chrome safari支持的方式
-ms-transform 是IE

-o-transform 是Opera

-moz-transform 是Firefox





### rotate() 旋转

旋转的角度

```
.div2{
    transform: rotate(180deg);
    -webkit-transform: rotate(180deg);
}
```

> 需要指定浏览器





### scale() 缩放

```
.div2{
    transform: scale(1,2); 
    -webkit-transform: scale(1,2);
}
```

> 参数1为宽度
>
> 参数2位倍数



### skew() 倾斜

```
.div2{
    transform: skew(20deg,20deg); 
    -webkit-transform: skew(20deg,20deg);
}
```

> 参数1为X轴
>
> 参数2位Y轴



### matrix() 矩阵效果





## 3D



### rotateX()

```
.div2{
    transform: rotateX(120deg);
    -webkit-transform: rotateX(120deg);
}
```



### rotateY()

```
.div2{
    transform: rotateY(120deg);
    -webkit-transform: rotateY(120deg);
}
```





## 过渡过程

### transition 过渡属性

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



#### transition-duration 效果时间



#### transition-timing-function 效果的时间曲线



#### transition-dely 延时开始时间





## animation

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





# example

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
   $('html,body').animate({ scrollTop: 0 }, 'slow'); // 慢速滚动
  document.documentElement.scrollTop = 0;
}
</script>

</body>
</html>

```

