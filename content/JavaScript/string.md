---
title: "string 字符串"
date: 2018-06-04 15:58
collection: 基本变量类型
---

[TOC]



# String



## 字符串定义

> 字符串是以单引号'或双引号"括起来的任意文本，比如`'abc'`，`"xyz"`等等

* `''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a`，`b`，`c`这3个字符

## 转译

* 如果字符串内部既包含`'`又包含`"`怎么办？可以用转义字符`\`来标识，比如：

  ```
  'I\'m \"OK\"!';
  ```

  >  表示的字符串内容是：`I'm "OK"!`

* 转义字符\可以转义很多字符，比如\n表示换行，\t表示制表符，字符\本身也要转义，所以\\表示的字符就是\。

  

### ASCII 字符

* ASCII字符可以以`\x##`形式的十六进制表示，例如：

```
'\x41'; // 完全等同于 'A'
```


### Unicode 字符

* 还可以用`\u####`表示一个Unicode字符：

  ```
  '\u4e2d\u6587'; // 完全等同于 '中文'
  ```



## 多行字符串

* 由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号 *`* ... *`* 表示：

```
`这是一个
多行
字符串`;
```



## 操作字符串



### 字符串连接

要把多个字符串连接起来，可以用`+`号连接：

```
var name='Rick';
var age=18;
var message = 'Hello, ' + name + ', your age is ' + age;
message
"Hello, Rick, your age is 18"
```


### 字符串长度

```
var s = 'Hello, world!';
s.length; // 13
```



### 字符串位置

要获取字符串某个指定位置的字符，使用类似Array的下标操作，索引号从0开始：

```
var s = 'Hello, world!';

s[0]; // 'H'
s[6]; // ' '
s[7]; // 'w'
s[12]; // '!'
s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined
```


#### 字符串索引赋值

字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：

```
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```


### toUpperCase

`toUpperCase()`把一个字符串全部变为大写：

```
var s = 'Hello';
s.toUpperCase(); // 返回'HELLO'
```



### toLowerCase

`toLowerCase()`把一个字符串全部变为小写：

```
var s = 'Hello';
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
lower; // 'hello'
```

### indexOf

`indexOf()`会搜索指定字符串出现的位置：

```
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1
```

### substring

`substring()`返回指定索引区间的子串：

```
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```

### length

```
var txt="Hello World";
txt.length
>>>
11
```



### match

```
var str = "Hello World";
str.match('World')
["World", index: 6, input: "Hello World"]
str.match('world')
null
```



### replace 

```
var str = "Hello World";
str.replace(/World/, 'Rick');
"Hello Rick"
str
"Hello World"
```

