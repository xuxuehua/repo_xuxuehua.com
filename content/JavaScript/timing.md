---
title: "timing"
date: 2018-07-27 23:20
---

[TOC]



# 计时器



## setTimeout()

暂停指定的毫秒数后执行指定代码



### 语法

```
setTimeout(function, milliseconds)
```

> function （必需）：要调用的函数后要执行的 JavaScript 代码串。
>
> millisec（必需）：在执行代码前需等待的毫秒数。 　

setTimeout() 只执行 code 一次。如果要多次调用，请使用 setInterval() 或者让 code 自身再次调用 setTimeout()。



```
const timeoutObj = setTimeout(() => {
  console.log('timeout beyond time');
}, 1500);
```





```
<html>
<head>
<script type="text/javascript">
function timedMsg()
{
var t=setTimeout("alert('5 秒！')",5000)
}
</script>
</head>

<body>
<form>
<input type="button" value="显示定时的警告框" onClick = "timedMsg()">
</form>
<p>请点击上面的按钮。警告框会在 5 秒后显示。</p>
</body>

</html>
```





### 无限循环

```
<html>
<head>
<script type="text/javascript">
var c=0
var t
function timedCount()
{
document.getElementById('txt').value=c
c=c+1
t=setTimeout("timedCount()",1000)
}
</script>
</head>

<body>

<form>
<input type="button" value="开始计时！" onClick="timedCount()">
<input type="text" id="txt">
</form>

<p>请点击上面的按钮。输入框会从 0 开始一直进行计时。</p>

</body>

</html>
```





## clearTimeout()

用于停止执行setTimeout()方法的函数代码



### 语法

clearTimeout(timeoutID)

要使用 clearTimeout( ), 我们设定 setTimeout( ) 时 , 要给予这 setTimout( ) 一个名称 , 这名称就是 timeoutID , 我们叫停时 , 就是用这 timeoutID 来叫停 , 这是一个自定义名称 , 但很多人就以 timeoutID 为名。



```
const timeoutObj = setTimeout(() => {
    console.log('timeout log');
});

clearTimeout(timeoutObj);
```





```
<html>

<head>
<script type="text/javascript">
var c=0
var t

function timedCount()
 {
 document.getElementById('txt').value=c
 c=c+1
 t=setTimeout("timedCount()",1000)
 }

function stopCount()
 {
 clearTimeout(t)
 }
</script>
</head>

<body>
<form>
<input type="button" value="Start count!" onClick="timedCount()">
<input type="text" id="txt">
<input type="button" value="Stop count!" onClick="stopCount()">
</form>
</body>

</html>
```



## setImmediate()

```
const immediateObj = setImmediate(() => {
    console.log('Immediate executing');
});
```




## setInterval()

setInterval() 方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。由 setInterval() 返回的 ID 值可用作 clearInterval() 方法的参数。



### 语法

**语法:** setInterval(code,millisec[,"lang"])

**参数:** code 必需。要调用的函数或要执行的代码串。millisec 必须。周期性执行或调用 code 之间的时间间隔，以毫秒计。

**返回值:** 一个可以传递给 Window.clearInterval() 从而取消对 code 的周期性执行的值。



```
const intervalObj = setInterval(() => {
    console.log('interval timeout');
});
```



## clearInterval()

停止setInterval() 方法执行的函数代码

```
const intervalObj = setInterval(() => {
    console.log('Interval timeout);
});

clearInterval(intervalObj);
```







## 区别

通过上面可以看出，setTimeout和setinterval的最主要区别是：

setTimeout只运行一次，也就是说设定的时间到后就触发运行指定代码，运行完后即结束。如果运行的代码中再次运行同样的setTimeout命令，则可循环运行。（即 要循环运行，需函数自身再次调用 setTimeout()）

而 setinterval是循环运行的，即每到设定时间间隔就触发指定代码。这是真正的定时器。

setinterval使用简单，而setTimeout则比较灵活，可以随时退出循环，而且可以设置为按不固定的时间间隔来运行，比如第一次1秒，第二次2秒，第三次3秒。

