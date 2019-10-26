---
title: "string"
date: 2019-10-20 12:20
---
[TOC]



# String

在Java中，`String`是一个引用类型，它本身也是一个`class`。但是，Java编译器对`String`有特殊处理，即可以直接用`"..."`来表示一个字符串



## 定义

```
String s1 = "Hello!";
```

字符串在`String`内部是通过一个`char[]`数组表示的，因此，按下面的写法也是可以的：

```
String s2 = new String(new char[] {'H', 'e', 'l', 'l', 'o', '!'});
```

因为`String`太常用了，所以Java提供了`"..."`这种字符串字面量表示方法



## 特点

Java字符串的一个重要特点就是字符串*不可变*。这种不可变性是通过内部的`private final char[]`字段，以及没有任何修改`char[]`的方法实现的

```
public class Main {
    public static void main(String[] args) {
        String s = "Hello";
        System.out.println(s);
        s = s.toUpperCase();
        System.out.println(s);
    }
}

>>>
Hello
HELLO
```





# 字符串处理 

## equals() 比较

当我们想要比较两个字符串是否相同时，要特别注意，我们实际上是想比较字符串的内容是否相同。必须使用`equals()`方法而不能用`==`

```
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "hello";
        String s3 = "HELLO".toLowerCase();
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
        System.out.println(s1 == s3);
        System.out.println(s1.equals(s3));
    }
}

>>>
true
true
false
true
```

> 从表面上看，两个字符串用`==`和`equals()`比较都为`true`，但实际上那只是Java编译器在编译期，会自动把所有相同的字符串当作一个对象放入常量池



### equalsIgnoreCase() 忽略大小写 

使用`equalsIgnoreCase()`方法



## contains() 包含

```
// 是否包含子串:
"Hello".contains("ll"); // true
```





## indexOf()

```
"Hello".indexOf("l"); // 2
```



## lastIndexOf()

```
"Hello".lastIndexOf("l"); // 3
```



## startsWith()

```
"Hello".startsWith("He"); // true
```



## endsWith()

```
"Hello".endsWith("lo"); // true
```



## substring() 提取

```
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); "ll"
```





## trim() 去除首尾空白字符

使用`trim()`方法可以移除字符串首尾空白字符。空白字符包括空格，`\t`，`\r`，`\n`：

```
"  \tHello\r\n ".trim(); // "Hello"
```

>  `trim()`并没有改变字符串的内容，而是返回了一个新字符串



## strip() 移除字符串首尾空白字符

`strip()`方法也可以移除字符串首尾空白字符。它和`trim()`不同的是，类似中文的空格字符`\u3000`也会被移除：

```
"\u3000Hello\u3000".strip(); // "Hello"
" Hello ".stripLeading(); // "Hello "
" Hello ".stripTrailing(); // " Hello"
```





## isEmpty()

```
"".isEmpty(); // true，因为字符串长度为0
"  ".isEmpty(); // false，因为字符串长度不为0
```



## isBlank()

```
"  \n".isBlank(); // true，因为只包含空白字符
" Hello ".isBlank(); // false，因为包含非空白字符
```



## replace() 

```
String s = "hello";
s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"
```



## replaceAll() 正则替换

```
String s = "A,,B;C ,D";
s.replaceAll("[\\,\\;\\s]+", ","); // "A,B,C,D"
```



## split() 

`split()`方法，并且传入的也是正则表达式

```
String s = "A,B,C,D";
String[] ss = s.split("\\,"); // {"A", "B", "C", "D"}
```





## join() 拼接

```
String[] arr = {"A", "B", "C"};
String s = String.join("***", arr); // "A***B***C"
```





## valueOf() 转换为字符串

要把任意基本类型或引用类型转换为字符串，可以使用静态方法`valueOf()`。这是一个重载方法，编译器会根据参数自动选择合适的方法

```
String.valueOf(123); // "123"
String.valueOf(45.67); // "45.67"
String.valueOf(true); // "true"
String.valueOf(new Object()); // 类似java.lang.Object@636be97c
```





## parseInt() 转换为int

```
int n1 = Integer.parseInt("123"); // 123
int n2 = Integer.parseInt("ff", 16); // 按十六进制转换，255
```



## parseBoolean 转换为boolean

```
boolean b1 = Boolean.parseBoolean("true"); // true
boolean b2 = Boolean.parseBoolean("FALSE"); // false
```



# String char[] 相互转换

`String`和`char[]`类型可以互相转换



## toCharArray() 

```
char[] cs = "Hello".toCharArray(); // String -> char[]
```





## String()

通过`new String(char[])`创建新的`String`实例时，它并不会直接引用传入的`char[]`数组，而是会复制一份，所以，修改外部的`char[]`数组不会影响`String`实例内部的`char[]`数组，因为这是两个不同的数组。

从`String`的不变性设计可以看出，如果传入的对象有可能改变，我们需要复制而不是直接引用

```
String s = new String(cs); // char[] -> String
```





# 字符编码

在早期的计算机系统中，为了给字符编码，美国国家标准学会（American National Standard Institute：ANSI）制定了一套英文字母、数字和常用符号的编码，它占用一个字节，编码范围从`0`到`127`，最高位始终为`0`，称为`ASCII`编码。例如，字符`'A'`的编码是`0x41`，字符`'1'`的编码是`0x31`。

如果要把汉字也纳入计算机编码，很显然一个字节是不够的。`GB2312`标准使用两个字节表示一个汉字，其中第一个字节的最高位始终为`1`，以便和`ASCII`编码区分开。例如，汉字`'中'`的`GB2312`编码是`0xd6d0`。

类似的，日文有`Shift_JIS`编码，韩文有`EUC-KR`编码，这些编码因为标准不统一，同时使用，就会产生冲突。

为了统一全球所有语言的编码，全球统一码联盟发布了`Unicode`编码，它把世界上主要语言都纳入同一个编码，这样，中文、日文、韩文和其他语言就不会冲突。

`Unicode`编码需要两个或者更多字节表示，我们可以比较中英文字符在`ASCII`、`GB2312`和`Unicode`的编码：

英文字符`'A'`的`ASCII`编码和`Unicode`编码：

```ascii
         ┌────┐
ASCII:   │ 41 │
         └────┘
         ┌────┬────┐
Unicode: │ 00 │ 41 │
         └────┴────┘
```

英文字符的`Unicode`编码就是简单地在前面添加一个`00`字节。

中文字符`'中'`的`GB2312`编码和`Unicode`编码：

```ascii
         ┌────┬────┐
GB2312:  │ d6 │ d0 │
         └────┴────┘
         ┌────┬────┐
Unicode: │ 4e │ 2d │
         └────┴────┘
```



## utf-8

英文字符的`Unicode`编码高字节总是`00`，包含大量英文的文本会浪费空间，所以，出现了`UTF-8`编码，它是一种变长编码，用来把固定长度的`Unicode`编码变成1～4字节的变长编码。通过`UTF-8`编码，英文字符`'A'`的`UTF-8`编码变为`0x41`，正好和`ASCII`码一致，而中文`'中'`的`UTF-8`编码为3字节`0xe4b8ad`。

`UTF-8`编码的另一个好处是容错能力强。如果传输过程中某些字符出错，不会影响后续字符，因为`UTF-8`编码依靠高字节位来确定一个字符究竟是几个字节，它经常用来作为传输编码



## 字符串转换编码

Java的`String`和`char`在内存中总是以Unicode编码表示

## char -> byte

`char`类型实际上就是两个字节的`Unicode`编码

```
byte[] b1 = "Hello".getBytes(); // 按ISO8859-1编码转换，不推荐
byte[] b2 = "Hello".getBytes("UTF-8"); // 按UTF-8编码转换
byte[] b2 = "Hello".getBytes("GBK"); // 按GBK编码转换
byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); // 按UTF-8编码转换
```

> 转换编码后，就不再是`char`类型，而是`byte`类型表示的数组



## byte -> char

```
byte[] b = ...
String s1 = new String(b, "GBK"); // 按GBK转换
String s2 = new String(b, StandardCharsets.UTF_8); // 按UTF-8转换
```



# StringBuilder

Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象

```
public class Main {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder(1024);
        for (int i=0; i<10; i++) {
            sb.append(",");
            sb.append(i);
        }
        String s = sb.toString();
        System.out.println(s);
    }
}

>>>
,0,1,2,3,4,5,6,7,8,9
```





## 链式操作

`StringBuilder`还可以进行链式操作

```
public class Main {
    public static void main(String[] args) {
        var sb = new StringBuilder(1024);
        sb.append("Mr ")
                .append("Xu")
                .append("!")
                .insert(0, "Hello, ");
        System.out.println(sb.toString());
    }
}

>>>
Hello, Mr Xu!
```





# StringJoiner 高效拼接字符串

要高效拼接字符串，应该使用`StringBuilder`

```
import java.util.StringJoiner;

public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sj = new StringJoiner(", ");
        for (String name: names) {
            sj.add(name);
        }
        System.out.println(sj.toString());
    }
}

>>>
Bob, Alice, Grace
```



## 指定“开头”和“结尾”

```
import java.util.StringJoiner;

public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sj = new StringJoiner(", ", "Hello ", "!");
        for (String name: names) {
            sj.add(name);
        }
        System.out.println(sj.toString());
    }
}

>>>
Hello Bob, Alice, Grace!
```



## 原理

那么`StringJoiner`内部是如何拼接字符串的呢？如果查看源码，可以发现，`StringJoiner`内部实际上就是使用了`StringBuilder`，所以拼接效率和`StringBuilder`几乎是一模一样的



## String.join() 无需指定“开头”和“结尾”

`String`还提供了一个静态方法`join()`，这个方法在内部使用了`StringJoiner`来拼接字符串，在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便

```
String[] names = {"Bob", "Alice", "Grace"};
var s = String.join(", ", names);
```

