---
title: "char 单个字符"
date: 2019-10-06 11:39
collection: 基本变量类型
---
[TOC]

# char (不常用)

字符类型`char`表示一个字符。

Java的`char`类型除了可表示标准的ASCII外，还可以表示一个Unicode字符

不建议在程序中使用char类型，除非确实需要处理UTF-16 代码，最好将字符串座位抽象数据类型处理

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



