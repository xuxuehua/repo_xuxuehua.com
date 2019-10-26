---
title: "char 字符"
date: 2019-10-06 11:39
collection: 基本变量类型
---
[TOC]

# char



字符类型`char`表示一个字符。Java的`char`类型除了可表示标准的ASCII外，还可以表示一个Unicode字符

一个`char`保存一个Unicode字符, 因为Java在内存中总是使用Unicode表示字符，所以，一个英文字符和一个中文字符都用一个`char`类型表示，它们都占用两个字节。要显示一个字符的Unicode编码，只需将`char`类型直接赋值给`int`类型即可





可以直接用转义字符`\u`+Unicode编码来表示一个字符：

```
// 注意是十六进制:
char c3 = '\u0041'; // 'A'，因为十六进制0041 = 十进制65
char c4 = '\u4e2d'; // '中'，因为十六进制4e2d = 十进制20013
```



`char`类型使用单引号`'`，且仅有一个字符，要和双引号`"`的字符串类型区分开





```
public class mychar {
    public static void main(String[] args) {
        char a = 'A';
        char zh = '中';
        System.out.println(a);
        System.out.println(zh);
    }
}

>>>
A
中
```



# String 字符串



和`char`类型不同，字符串类型`String`是引用类型，我们用双引号`"..."`表示字符串。一个字符串可以存储0个到任意个字符

```
String s = ""; // 空字符串，包含0个字符
String s1 = "A"; // 包含一个字符
String s2 = "ABC"; // 包含3个字符
String s3 = "中文 ABC"; // 包含6个字符，其中有一个空格
```



## 转义

如果字符串本身恰好包含一个`"`字符，编译器就无法判断中间的引号究竟是字符串的一部分还是表示字符串结束。这个时候，我们需要借助转义字符`\`

```
String s = "abc\"xyz"; // 包含7个字符: a, b, c, ", x, y, z
```



### 常见的转义字符

- `\"` 表示字符`"`
- `\'` 表示字符`'`
- `\\` 表示字符`\`
- `\n` 表示换行符
- `\r` 表示回车符
- `\t` 表示Tab
- `\u####` 表示一个Unicode编码的字符



## 字符串连接

Java的编译器对字符串做了特殊照顾，可以使用`+`连接任意字符串和其他数据类型

```
public class Main {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "world";
        String s = s1 + " " + s2 + "!";
        System.out.println(s);
    }
}

>>>
Hello world!
```



如果用`+`连接字符串和其他数据类型，会将其他数据类型先自动转型为字符串



### 多行字符串连接

如果我们要表示多行字符串，使用+号连接会非常不方便：

```
String s = "first line \n"
         + "second line \n"
         + "end";
```

从Java 13开始，字符串可以用`"""..."""`表示多行字符串（Text Blocks）了

由于多行字符串是作为Java 13的预览特性（Preview Language Features）实现的，编译的时候，我们还需要给编译器加上参数：

```
javac --source 13 --enable-preview Main.java
```



开启preview 模式

![img](https://d3nmt5vlzunoa1.cloudfront.net/idea/files/2019/02/Switchexpr-IDEsettings.gif)



```
public class Main {
    public static void main(String[] args) {
        String s = """
                    select * from
                    users
                    where id > 100
                    order by name desc
                    """;
        System.out.println(s);
    }
}

>>>
select * from
users
where id > 100
order by name desc
```



## null

引用类型的变量可以指向一个空值`null`，它表示不存在，即该变量不指

```
String s1 = null; // s1是null
String s2; // 没有赋初值值，s2也是null
String s3 = s1; // s3也是null
String s4 = ""; // s4指向空字符串，不是null
```

