---
title: "bool 布尔计算"
date: 2018-06-04 16:03
collection: 基本变量类型
---

[TOC]

# Bool





## 布尔值

* 布尔值和布尔代数的表示完全一致，一个布尔值只有`true`、`false`两种值，要么是`true`，要么是`false`



### 声明布尔对象

```
var y = new Boolean;
```



#### 初始值为 false

```
var myBoolean=new Boolean();
var myBoolean=new Boolean(0);
var myBoolean=new Boolean(null);
var myBoolean=new Boolean("");
var myBoolean=new Boolean(false);
var myBoolean=new Boolean(NaN);
```





#### 初始值为 True

```
var myBoolean=new Boolean(1);
var myBoolean=new Boolean(true);
var myBoolean=new Boolean("true");
var myBoolean=new Boolean("false");
var myBoolean=new Boolean("Bill Gates");
```





## 布尔计算

```
true; // 这是一个true值
false; // 这是一个false值
2 > 1; // 这是一个true值
2 >= 3; // 这是一个false值
```



### &&与运算

* `&&`运算是与运算，只有所有都为`true`，`&&`运算结果才是`true`

```
true && true; // 这个&&语句计算结果为true
true && false; // 这个&&语句计算结果为false
false && true && false; // 这个&&语句计算结果为false
```



### || 或运算

* `||`运算是或运算，只要其中有一个为`true`，`||`运算结果就是`true`

```
false || false; // 这个||语句计算结果为false
true || false; // 这个||语句计算结果为true
false || true || false; // 这个||语句计算结果为true
```



### ! 非运算

* `!`运算是非运算，它是一个单目运算符，把`true`变成`false`，`false`变成`true`

```
! true; // 结果为false
! false; // 结果为true
! (2 > 5); // 结果为true
```



#### ! !

第一个表示无论什么操作都返回一个布尔值

第二个表示对布尔值取反



## 与其他类型比较

### 与数据类型比较

* JavaScript允许对任意数据类型做比较

  > 第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；
  >
  > 第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。
  >
  > 由于JavaScript这个设计缺陷，*不要*使用`==`比较，始终坚持使用`===`比较

```
false == 0; // true
false === 0; // false
```



### 与浮点数比较

```
1 / 3 === (1 - 2 / 3); // false
```

* 浮点数在运算过程中会产生误差，因为计算机无法精确表示无限循环小数。要比较两个浮点数是否相等，只能计算它们之差的绝对值，看是否小于某个阈值：

```
Math.abs(1 / 3 - (1 - 2 / 3)) < 0.0000001; // true
```



### 与NaN比较

* `NaN`这个特殊的Number与所有其他值都不相等，包括它自己

```
NaN === NaN; // false
```

* 唯一能判断`NaN`的方法是通过`isNaN()`函数：

```
isNaN(NaN); // true
```



