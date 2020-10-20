---
title: "number 数字"
date: 2018-06-04 11:35
collection: 基本变量类型
---



[TOC]



# Number



## 定义

JavaScript 不是类型语言。与许多其他编程语言不同，JavaScript 不定义不同类型的数字，比如整数、短、长、浮点等等。

JavaScript 中的所有数字都存储为根为 10 的 64 位（8 比特），浮点数。



### 精度

整数（不使用小数点或指数计数法）最多为 15 位。

小数的最大位数是 17，但是浮点运算并不总是 100% 准确：

 

### 八进制和十六进制

如果前缀为 0，则 JavaScript 会把数值常量解释为八进制数，如果前缀为 0 和 "x"，则解释为十六进制数。

```
var y=0377;
var z=0xFF;
```

提示：绝不要在数字前面写零，除非您需要进行八进制转换。



* JavaScript不区分整数和浮点数，统一用Number表示，以下都是合法的Number类型

```
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
var z=123e-5;   // 0.00123
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```



## 四则运算

* y=5

| +    | 加                | x=y+2 | x=7   |
| ---- | ----------------- | ----- | ----- |
| -    | 减                | x=y-2 | x=3   |
| *    | 乘                | x=y*2 | x=10  |
| /    | 除                | x=y/2 | x=2.5 |
| %    | 求余数 (保留整数) | x=y%2 | x=1   |
| ++   | 累加              | x=++y | x=6   |
| --   | 递减              | x=--y | x=4   |



### 除法

```
2 / 0;  // Infinity
0 / 0; NaN
(1 + 2) * 5 / 2;  // 7.5
```



### 求余

* 只保留整数

```
10 % 3; // 1
10.5 % 3 // 1.5
```



### 数字与字符串相加

```
<html>
<body>

<script type="text/javascript">
x=5+5;
document.write(x);
document.write("<br />");
x="5"+"5";
document.write(x);
document.write("<br />");
x=5+"5";
document.write(x);
document.write("<br />");
x="5"+5;
document.write(x);
document.write("<br />");
</script>

<h3>规则是：</h3>

<p><strong>如果把数字与字符串相加，结果将成为字符串。</strong></p>

</body>
</html>
>>>
10
55
55
55
```



### 



## 比较运算

给定 x=5，下面的表格解释了比较运算符：

| 运算符 | 描述             | 例子                            |
| ------ | ---------------- | ------------------------------- |
| ==     | 等于             | x==8 为 false                   |
| ===    | 全等（值和类型） | x===5 为 true；x==="5" 为 false |
| !=     | 不等于           | x!=8 为 true                    |
| >      | 大于             | x>8 为 false                    |
| <      | 小于             | x<8 为 true                     |
| >=     | 大于或等于       | x>=8 为 false                   |
| <=     | 小于或等于       | x<=8 为 true                    |



对Number做比较时，可以通过比较运算符得到一个布尔值

```
2 > 5; // false
5 >= 2; // true
7 == 7; // true
```





## 逻辑运算符

给定 x=6 以及 y=3，下表解释了逻辑运算符：

| 运算符 | 描述 | 例子                      |
| ------ | ---- | ------------------------- |
| &&     | and  | (x < 10 && y > 1) 为 true |
| \|\|   | or   | (x==5 \|\| y==5) 为 false |
| !      | not  | !(x==y) 为 true           |





## 数字属性和方法

### 属性

- MAX VALUE
- MIN VALUE
- NEGATIVE INFINITIVE
- POSITIVE INFINITIVE
- NaN
- prototype
- constructor

### 方法

- toExponential()
- toFixed()
- toPrecision()
- toString()
- valueOf()





## 算数对象



### round() 四舍五入

```
<html>
<body>

<script type="text/javascript">

document.write(Math.round(0.60) + "<br />")
document.write(Math.round(0.50) + "<br />")
document.write(Math.round(0.49) + "<br />")
document.write(Math.round(-4.40) + "<br />")
document.write(Math.round(-4.60))

</script>

</body>
</html>

>>>
1
1
0
-4
-5
```



### random()

random() 方法来返回一个介于 0 和 1 之间的随机数

```
Math.random()
0.28042991131344963
Math.random()
0.08264409713072896
Math.random(1)
0.7369426245073265
Math.random(100)
0.2889252689793813
```



 random() 来返回一个介于 0 和 10 之间的随机数

```
Math.random()*11
1.074765324582574
Math.random()*11
10.042596148277743
Math.random()*11
6.226359473942004
```



获取随机数

```
function getRandomInt(max) {
  return Math.floor(Math.random() * Math.floor(max));
}

console.log(getRandomInt(3));
// expected output: 0, 1 or 2

console.log(getRandomInt(1));
// expected output: 0

console.log(Math.random());
// expected output: a number between 0 and 1
```



### max()

```
<html>
<body>

<script type="text/javascript">

document.write(Math.max(5,7) + "<br />")
document.write(Math.max(-3,5) + "<br />")
document.write(Math.max(-3,-5) + "<br />")
document.write(Math.max(7.25,7.30))

</script>

</body>
</html>

>>>
7
5
-3
7.3
```



### min()

```
<html>
<body>

<script type="text/javascript">

document.write(Math.min(5,7) + "<br />")
document.write(Math.min(-3,5) + "<br />")
document.write(Math.min(-3,-5) + "<br />")
document.write(Math.min(7.25,7.30))

</script>

</body>
</html>

>>>
5
-3
-5
7.25
```



## 算数值

JavaScript 提供 8 种可被 Math 对象访问的算数值：

- 常数
- 圆周率
- 2 的平方根
- 1/2 的平方根
- 2 的自然对数
- 10 的自然对数
- 以 2 为底的 e 的对数
- 以 10 为底的 e 的对数

这是在 Javascript 中使用这些值的方法：（与上面的算数值一一对应）

- Math.E
- Math.PI
- Math.SQRT2
- Math.SQRT1_2
- Math.LN2
- Math.LN10
- Math.LOG2E
- Math.LOG10E







