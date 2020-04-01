---
title: "circulation 循环"
date: 2018-06-09 22:14
---

[TOC]

# Circulation



## for 循环

```
for (语句 1; 语句 2; 语句 3)
  {
  被执行的代码块
  }
```

> 语句 1 在循环（代码块）开始前执行
>
> 语句 2 定义运行循环（代码块）的条件
>
>语句 3 在循环（代码块）已被执行之后执行 



通过初始条件、结束条件和递增条件来循环执行语句块

```
var x = 0;
var i;
for (i=1; i<=10000; i++) {
    x = x + i;
}
x; // 50005000
```



### 省略语句1

```
cars = ['Benz', 'BMW', 'Audi', 'Ferrari'];
var i=2,len=cars.length;
for (; i<len; i++)
{
console.log(cars[i] + "<br>");
}
>>>
VM73:4 Audi<br>
VM73:4 Ferrari<br>
```



### 省略语句2

通常语句 2 用于评估初始变量的条件。

语句 2 同样是可选的。

如果语句 2 返回 true，则循环再次开始，如果返回 false，则循环将结束。

> 如果您省略了语句 2，那么必须在循环内提供 break。否则循环就无法停下来。这样有可能令浏览器崩溃



### 省略语句3

通常语句 3 会增加初始变量的值。

语句 3 也是可选的。

语句 3 有多种用法。增量可以是负数 (i--)，或者更大 (i=i+15)。

语句 3 也可以省略（比如当循环内部有相应的代码时）

```
cars=["BMW","Volvo","Saab","Ford"];
var i=0,len=cars.length;
for (; i<len; )
{
console.log(cars[i] + "<br>");
i++;
}
>>>
BMW
Volvo
Saab
Ford
```







### 无限循环

* 没有退出循环的判断条件，就必须使用`break`语句退出循环，否则就是死循环

```
var x = 0;
for (;;) { // 将无限循环下去
    if (x > 100) {
        break; // 通过if判断来退出循环
    }
    x ++;
}

```





### for 循环遍历数组

```
var arr = ['Apple', 'Google', 'Microsoft'];
var i, x;
for (i=0; i<arr.length; i++) {
    x = arr[i];
    console.log(x);
}
```



## for ... in 循环

* 可以把一个对象的所有属性依次循环出来

```
var o = {
    name: "Jack",
    age: 20,
    city: "Beijing"
};

for (var key in o) {
    console.log(key); // 'name', 'age', 'city'
}
```



### for ... in 过滤对象属性

* 用`hasOwnProperty()`来实现

```
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    if (o.hasOwnProperty(key)) {
        console.log(key); // 'name', 'age', 'city'
    }
}
```



### for ... in 循环Array的索引

* 由于`Array`也是对象，而它的每个元素的索引被视为对象的属性

```
var a = ['A', 'B', 'C'];
for (var i in a) {
    console.log(i); // '0', '1', '2' 这个是string
    console.log(a[i]); // 'A', 'B', 'C'
}
```

> * `for ... in`对`Array`的循环得到的是`String`而不是`Number`



### for ... in 循环对象value

```
var o = {
    name: "Jack",
    age: 20,
    city: "Beijing"
};
var i;
for (i in o) {
console.log(o[i])
}
>>
VM131:1 Jack
VM131:1 20
VM131:1 Beijing
```





## while 循环

`while`循环只有一个判断条件，条件满足，就不断循环，条件不满足时则退出循环

```
var x = 0;
var n = 99;
while (n > 0) {
    x = x + n;
    n = n - 2;
}
x; // 2500
```

```
var myFunc01 = function() {
  var i = 0;
  while (i < 100) {
    (function(i) {
      setTimeout(function() {
        document.getElementById('d01').innerHTML += 100 - i + "<br>";
      }, 1000 * i)
    })(i++)
  }
};

myFunc01();
```



## do while 循环

* 和`while`循环的唯一区别在于，不是在每次循环开始的时候判断条件，而是在每次循环完成的时候判断条件

```
var n = 0;
do {
    n = n + 1;
} while (n < 100);
n; // 100
```

> 用`do { ... } while()`循环要小心，循环体会至少执行1次，而`for`和`while`循环则可能一次都不执行



## for ... of 循环

* 具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历



### for ... of 遍历集合

```
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x);
}
for (var x of s) { // 遍历Set
    console.log(x);
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}
```



### for ... of 修复 for ... in 问题

* `for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0', '1', '2', 'name'
}
```

* `for ... in`循环将把`name`包括在内，但`Array`的`length`属性却不包括在内。
* `for ... of`循环则完全修复了这些问题，它只循环集合本身的元素：

```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    console.log(x); // 'A', 'B', 'C'
}
```



## forEach()方法 (更好的方式)

* `forEach`方法是iterable`内置的，它接收一个函数，每次迭代就自动回调该函数

```
'use strict';
var a = ['A', 'B', 'C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
>>>
A, index = 0
B, index = 1
C, index = 2
```



### 作用于Set

* `Set`与`Array`类似，但`Set`没有索引，因此回调函数的前两个参数都是元素本身：

```
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
>>>
A
B
C
```



### 作用于Map

* `Map`的回调函数参数依次为`value`、`key`和`map`本身：

```
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    console.log(value);
});
>>>
x
y
z
```



### 作用于Array

```
var a = ['A', 'B', 'C'];
a.forEach(function (element) {
    console.log(element);
});
>>>
A
B
CÁ
```





## break 

**break 语句用于跳出循环。**



## continue 

**continue 用于跳过循环中的一个迭代。**

