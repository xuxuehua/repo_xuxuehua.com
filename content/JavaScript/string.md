---
title: "string 字符串"
date: 2018-06-04 15:58
collection: 基本变量类型
---

[TOC]



# String



字符串是以单引号'或双引号"括起来的任意文本，比如`'abc'`，`"xyz"`等等

`''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a`，`b`，`c`这3个字符

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

### substring 切片

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



### endsWith

```
var str = "Hello world, welcome to the universe.";
var n = str.endsWith("universe.");

>>>
true
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

利用`replace()`进行替换的时候，如果传入的是字符串，则只会替换第一个子字符串，要想替换所有的子字符串，则需要传入一个正则表达式，而且要指定全局（g）标志

```
var text = 'cat , bat , sat , fat';
var result = text.replace('at','ond');
console.log(result); // =>'cont , bat , sat , fat'

result = text.replace(/at/g,'ond');
console.log(result); //=>'cont , bont , sont , font'
```



```
var str = "Hello World";
str.replace(/World/, 'Rick');
"Hello Rick"
str
"Hello World"
```



### toString 转换为字符串

可以是数值， 布尔值，对象和字符串

```
str.toString()
```





### tri 去除空格

ES5中新增`trim()`方法用于去除字符串的左右空格，**该方法会创建一个字符串的副本，不会改变原有的字符串**，此外，Firefox 3.5+、Safari 5+
和 Chrome 8+还支持非标准的 trimLeft()和 trimRight()方法，分别用于删除字符串开头和末尾的
空格。

其实去空格可以使用正则去匹配的去掉，这里写一个去空格函数

```
/*trim    去掉空白
str要处理的字符串        
[type]     类型：l 去除左边的空白    r去除右边空白    b去掉两边的空白        a去除所有空白*/
function trim (str,type) {
    var type=type||"b";
    if(type=="b"){
        return str.replace(/^\s*|\s*$/g,"");
    }else if(type=="l"){
        return str.replace(/^\s*/g,"");
    }else if(type=="r"){
        return str.replace(/\s*$/g,"");
    }else if(type=="a"){
        return str.replace(/\s*/g,"");
    }
}
```





## string to number 

```
<html>
<body>
<script>
   var a = "10";
   var b = parseInt(a);
   document.write("value is " + b);
   var c = parseInt("423-0-567");
   document.write("</br>");
   document.write('value is ' + c);
   document.write("</br>");
   var d = "string";
   var e = parseInt(d);
   document.write("value is " + e);
   document.write("</br>");
   var f = parseInt("2string");
   document.write("value is " + f);
</script>
</body>
</html>

>>>
value is 10
value is 423
value is NaN
value is 2
```

