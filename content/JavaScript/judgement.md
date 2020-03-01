---
title: "judgement 条件判断"
date: 2018-06-05 17:57
---

[TOC]

# Judgement





## If 语法

JavaScript使用`if () { ... } else { ... }`来进行条件判断

```
if (条件)
  {
  只有当条件为 true 时执行的代码
  }

```

其中`else`语句是可选的。如果语句块只包含一条语句，尽管可以省略`{}`, 但还是不要避免错误

```
var age = 20;
if (age >= 18) { // 如果age >= 18为true，则执行if语句块
    alert('adult');
} else { // 否则执行else语句块
    alert('teenager');
}
```



### 多行条件判断

```
if (条件 1)
  {
  当条件 1 为 true 时执行的代码
  }
else if (条件 2)
  {
  当条件 2 为 true 时执行的代码
  }
else
  {
  当条件 1 和 条件 2 都不为 true 时执行的代码
  }
```



如果还要更细致地判断条件，可以使用多个`if...else...`的组合：

```
var age = 3;
if (age >= 18) {
    alert('adult');
} else if (age >= 6) {
    alert('teenager');
} else {
    alert('kid');
}
```


### 默认判定

JavaScript把`null`、`undefined`、`0`、`NaN`和空字符串`''`视为`false`，其他值一概视为`true`





## Switch 语句

使用 switch 语句来选择要执行的多个代码块之一

```
switch(n)
{
case 1:
  执行代码块 1
  break;
case 2:
  执行代码块 2
  break;
default:
  n 与 case 1 和 case 2 不同时执行的代码
}
```

> 首先设置表达式 n（通常是一个变量）。随后表达式的值会与结构中的每个 case 的值做比较。如果存在匹配，则与该 case 关联的代码块会被执行。请使用 *break* 来阻止代码自动地向下一个 case 运行。



```
<!DOCTYPE html>
<html>
<body>

<p>点击下面的按钮来显示今天是周几：</p>

<button onclick="myFunction()">点击这里</button>

<p id="demo"></p>

<script>
function myFunction()
{
var x;
var d=new Date().getDay();
switch (d)
  {
  case 0:
    x="Today it's Sunday";
    break;
  case 1:
    x="Today it's Monday";
    break;
  case 2:
    x="Today it's Tuesday";
    break;
  case 3:
    x="Today it's Wednesday";
    break;
  case 4:
    x="Today it's Thursday";
    break;
  case 5:
    x="Today it's Friday";
    break;
  case 6:
    x="Today it's Saturday";
    break;
  }
document.getElementById("demo").innerHTML=x;
}
</script>

</body>
</html>

>>>
Today it's Saturday
```



### default 关键词

使用 default 关键词来规定匹配不存在时做的事情

```
<html>
<body>

<p>点击下面的按钮，会显示出基于今日日期的消息：</p>

<button onclick="myFunction()">点击这里</button>

<p id="demo"></p>

<script>
function myFunction()
{
var x;
var d=new Date().getDay();
switch (d)
  {
  case 6:
    x="Today it's Saturday";
    break;
  case 0:
    x="Today it's Sunday";
    break;
  default:
    x="Looking forward to the Weekend";
  }
document.getElementById("demo").innerHTML=x;
}
</script>

</body>
</html>

```

