---
title: "basic_knowledge 基本知识"
date: 2018-06-04 11:03
---

[TOC]

# Basic knowledge



## 基本语法

* 每个语句以`;`结束，语句块用`{...}`

> JavaScript并不强制要求在每个语句的结尾加`;`，浏览器中负责执行JavaScript代码的引擎会自动在每个语句的结尾补上`;`
>
> * 但是让JavaScript引擎自动加分号在某些情况下会改变程序的语义，导致运行结果与期望不一致。



* 花括号`{...}`内的语句具有缩进，通常是4个空格。缩进不是JavaScript语法要求必须的，但缩进有助于我们理解代码的层次，



### 注释

* 以`//`开头直到行末的字符被视为行注释，注释是给开发人员看到，JavaScript引擎会自动忽略：
* 另一种块注释是用`/*...*/`把多行字符包裹起来，把一大“块”视为一个注释



### 大小写

* 请注意，JavaScript严格区分大小写，如果弄错了大小写，程序将报错或者运行不正常。



### 对代码行进行折行

您可以在文本字符串中使用反斜杠对代码行进行换行。下面的例子会正确地显示：

```
document.write("Hello \
World!");
```



### 代码位置

HTML 中的脚本必须位于 <script> 与 </script> 标签之间。

脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。

通常的做法是把函数放入 <head> 部分中，或者放在页面底部。这样就可以把它们安置到同一处位置，不会干扰页面的内容。



### 变量

**变量是存储信息的容器**

变量可以使用短名称（比如 x 和 y），也可以使用描述性更好的名称（比如 age, sum, totalvolume）。

- 变量必须以字母开头
- 变量也能以 $ 和 _ 符号开头（不过我们不推荐这么做）
- 变量名称对大小写敏感（y 和 Y 是不同的变量）



#### 声明（创建） JavaScript 变量

使用 var 关键词来声明变量：

```
var carname;
```

变量声明之后，该变量是空的（它没有值）。

如需向变量赋值，请使用等号：

```
carname="Volvo";
```

不过，您也可以在声明变量时对其赋值：

```
var carname="Volvo";
```

可以在一条语句中声明很多变量。该语句以 var 开头，并使用逗号分隔变量即可：

```
var name="Gates", age=56, job="CEO";
```

声明也可横跨多行：

```
var name="Gates",
age=56,
job="CEO";
```



### 简单操作

#### 写入 HTML 输出

```
<html>
<head>
    <title>Hello World</title>
</head>
<body>
<script>
    document.write('<h2>This is H2</h2>')
    document.write('<p>This is H2</p>')
</script>
</body>
</html>
```



* 如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖

```
<!DOCTYPE html>
<html>
<body>

<h1>My First Web Page</h1>

<p>My First Paragraph.</p>

<button onclick="myFunction()">点击这里</button>

<script>
function myFunction()
{
document.write("糟糕！文档消失了。");
}
</script>

</body>
</html>
```







#### 对事件作出反应

```
<!DOCTYPE html>
<html>
<body>

<h1>我的第一段 JavaScript</h1>

<p>
JavaScript 能够对事件作出反应。比如对按钮的点击：
</p>

<button type="button" onclick="alert('Welcome!')">点击这里</button>

</body>
</html>

```



#### 改变 HTML 内容

```
<!DOCTYPE html>
<html>
<body>

<h1>我的第一段 JavaScript</h1>

<p id="demo">
JavaScript 能改变 HTML 元素的内容。
</p>

<script>
function myFunction()
{
x=document.getElementById("demo");  // 找到元素
x.innerHTML="Hello World!";    // 改变内容
}
</script>

<button type="button" onclick="myFunction()">点击这里</button>

</body>
</html>
```



#### 改变 HTML 图像

```
<!DOCTYPE html>
<html>
<body>
<script>
function changeImage()
{
element=document.getElementById('myimage')
if (element.src.match("bulbon"))
  {
  element.src="/i/eg_bulboff.gif";
  }
else
  {
  element.src="/i/eg_bulbon.gif";
  }
}
</script>

<img id="myimage" onclick="changeImage()" src="/i/eg_bulboff.gif">

<p>点击灯泡来点亮或熄灭这盏灯</p>

</body>
</html>
```



#### 改变 HTML 样式

```
<!DOCTYPE html>
<html>
<body>

<h1>我的第一段 JavaScript</h1>

<p id="demo">
JavaScript 能改变 HTML 元素的样式。
</p>

<script>
function myFunction()
{
x=document.getElementById("demo") // 找到元素
x.style.color="#ff0000";          // 改变样式
}
</script>

<button type="button" onclick="myFunction()">点击这里</button>

</body>
</html>
```



#### 验证输入

```
<!DOCTYPE html>
<html>
<body>

<h1>我的第一段 JavaScript</h1>

<p>请输入数字。如果输入值不是数字，浏览器会弹出提示框。</p>

<input id="demo" type="text">

<script>
function myFunction()
{
var x=document.getElementById("demo").value;
if(x==""||isNaN(x))
	{
	alert("Not Numeric");
	}
}
</script>

<button type="button" onclick="myFunction()">点击这里</button>

</body>
</html>
```







## 外部Javascript

外部 JavaScript 文件的文件扩展名是 .js。

如需使用外部文件，请在 <script> 标签的 "src" 属性中设置该 .js 文件

```
<!DOCTYPE html>
<html>
<body>

<h1>My Web Page</h1>

<p id="demo">A Paragraph.</p>

<button type="button" onclick="myFunction()">点击这里</button>

<p><b>注释：</b>myFunction 保存在名为 "myScript.js" 的外部文件中。</p>

<script type="text/javascript" src="/js/myScript.js"></script>

</body>
</html>

```





## Null & undefined 

* `null`表示一个“空”的值，它和`0`以及空字符串`''`不同，`0`是一个数值，`''`表示长度为0的字符串，而`null`表示“空”。

* JavaScript的设计者希望用`null`表示一个空的值，而`undefined`表示值未定义。事实证明，这并没有什么卵用，区分两者的意义不大。大多数情况下，我们都应该用`null`。`undefined`仅仅在判断函数参数是否传递的情况下有用。

* 可以通过将变量的值设置为 null 来清空变量。

  ```
  cars=null;
  person=null;
  ```





## 变量

### 变量定义

* 变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。申明一个变量用`var`语句

```
var a; // 申明了变量a，此时a的值为undefined
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
var s_007 = '007'; // s_007是一个字符串
var Answer = true; // Answer是一个布尔值true
var t = null; // t的值是null
```



### 变量赋值

* 在JavaScript中，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`申明一次

```
var a = 123; // a的值是整数123
a = 'ABC'; // a变为字符串
```

#### 静态语言定义

> 这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言
>
> ```
> int a = 123; // a是整数类型变量，类型用int申明
> a = "ABC"; // 错误：不能把字符串赋给整型变量
> ```



## strict 模式

* JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为全局变量：

  ```
  i = 10; // i现在是全局变量
  ```

> 在同一个页面的不同的JavaScript文件中，如果都不用`var`申明，恰好都使用了变量`i`，将造成变量`i`互相影响，产生难以调试的错误结果。
>
> 使用`var`申明的变量则不是全局变量，它的范围被限制在该变量被申明的函数体内，同名变量在不同的函数体内互不冲突。

* 为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制通过`var`申明变量，未使用`var`申明变量就使用的，将导致运行错误。



### 开启strict模式

* 启用strict模式的方法是在JavaScript代码的第一行写上：

  ```
  'use strict';
  ```





## 消息框



### **警告框**

```
alert("我是警告框！！")
```



#### 带有折行的警告框

```
alert("再次向您问好！在这里，我们向您演示" + '\n' + "如何向警告框添加折行。")
```





### **确认框**

```
<html>
<head>
<script type="text/javascript">
function show_confirm()
{
var r=confirm("Press a button!");
if (r==true)
  {
  alert("You pressed OK!");
  }
else
  {
  alert("You pressed Cancel!");
  }
}
</script>
</head>
<body>

<input type="button" onclick="show_confirm()" value="Show a confirm box" />

</body>
</html>
```





### **提示框**

```
<html>
<head>
<script type="text/javascript">
function disp_prompt()
  {
  var name=prompt("请输入您的名字","Bill Gates")
  if (name!=null && name!="")
    {
    document.write("你好！" + name + " 今天过得怎么样？")
    }
  }
</script>
</head>
<body>

<input type="button" onclick="disp_prompt()" value="显示提示框" />

</body>
</html>

```











