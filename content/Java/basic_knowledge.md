---
title: "basic_knowledge"
date: 2019-11-02 11:09
---
[TOC]




# Java

Java介于编译型语言和解释型语言之间

Java是将代码编译成一种“字节码”，它类似于抽象的CPU指令，然后，针对不同平台编写虚拟机，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果





## Installation

### mac

download jdk 13

```
vim ~/.bash_profile
export JAVA_HOME=`/usr/libexec/java_home -v 13`
export PATH=$JAVA_HOME/bin:$PATH
```





# Java基本数据类型占用的字节数

```ascii
       ┌───┐
  byte │   │ # 一个字节
       └───┘
       ┌───┬───┐
 short │   │   │
       └───┴───┘
       ┌───┬───┬───┬───┐
   int │   │   │   │   │
       └───┴───┴───┴───┘
       ┌───┬───┬───┬───┬───┬───┬───┬───┐
  long │   │   │   │   │   │   │   │   │
       └───┴───┴───┴───┴───┴───┴───┴───┘
       ┌───┬───┬───┬───┐
 float │   │   │   │   │
       └───┴───┴───┴───┘
       ┌───┬───┬───┬───┬───┬───┬───┬───┐
double │   │   │   │   │   │   │   │   │
       └───┴───┴───┴───┴───┴───┴───┴───┘
       ┌───┬───┐
  char │   │   │
       └───┴───┘
```







# Hello World

```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

> `void`是方法的返回类型
>
> 文件名和类名一定要一致
>
> 使用`javac`可以将`.java`源码编译成`.class`字节码；
>
> 使用`java`可以运行一个已编译的Java程序，参数是类名



# 特点

每个java应用程序都必须有一个main 方法

# 注释

## 单行注释

以双斜线开头，直到这一行的结尾结束：

```
// 这是注释...
```



## 多行注释

以`/*`星号开头，以`*/`结束，可以有多行：

```
/*
这是注释
blablabla...
这也是注释
*/
```



## 特殊的多行注释

以`/**`开头，以`*/`结束，如果有多行，每行通常以星号开头：

```
/**
 * 可以用来自动创建文档的注释
 * 
 * @auther liaoxuefeng
 */
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```



# 变量

声明变量时，变量类型位于变量名之前

```
double salary;
int vacations;
long earthPopulation;
boolean done;
```



# 常量 final 

定义变量的时候，如果加上`final`修饰符，这个变量就变成了常量

final 表示的这个变量只能被赋值一次

常量在定义时进行初始化后就不可再次赋值，再次赋值会导致编译错误

```
final double PI = 3.14; // PI是一个常量
double r = 5.0;
double area = PI * r * r;
PI = 300; // compile error!
```



## 类常量 static final

使用static final 设置一个类常量

```
public class Main {
		public static final double CM_PER_INCH = 2.54;
		
		public static void main(String[] args) {
				double paperWidth = 8.5;
				double paperHeight = 11;
				System.out.println("Paper size in centimeters: " + paperWidth * CM_PER_INCH + " by " + paperHeight * CM_PER_INCH);
		}
}

>>>
Paper size in centimeters: 21.59 by 27.94
```





# 输入 Scanner

从控制台读取一个字符串和一个整数的

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in); // 创建Scanner对象, 通过控制台输入
        System.out.print("Input your name: "); // 打印提示
        String name = scanner.nextLine(); // 读取一行输入并获取字符串
        System.out.print("Input your age: "); // 打印提示
        int age = scanner.nextInt(); // 读取一行输入并获取整数
        System.out.printf("Hi, %s, you are %d\n", name, age); // 格式化输出
    }
}

>>>
Input your name: Rick
Input your age: 18
Hi, Rick, you are 18
```





## 常用方法



| 方法          | 用途                                     |
| ------------- | ---------------------------------------- |
| nextLine      | 读取输入的下一行内容                     |
| next          | 读取下一个单词，以空格作为分隔符         |
| nextInt       | 读取下一个                               |
| nextDouble    | 读取下一个浮点数                         |
| hasNext       | 检测输入中是否还有其他单词               |
| hasNextInt    |                                          |
| hasNextDouble | 是否还有表示浮点数或整数的下一个字符序列 |







# 输出 

println 表示 print line ， 即输出并换行，如果不想换行使用print()



## 格式化输出

格式化输出使用`System.out.printf()`，通过使用占位符`%?`，`printf()`可以把后面的参数格式化成指定格式

Java的格式化功能提供了多种占位符，可以把各种数据类型“格式化”成指定的字符串



| 占位符 | 说明                   |
| :----- | :--------------------- |
| %d     | 十进制整数             |
| %x     | 十六进制整数           |
| %o     | 八进制整数             |
| %f     | 定点浮点数             |
| %e     | 科学计数法表示的浮点数 |
| %s     | 字符串                 |
| %c     | 字符                   |
| %b     | 布尔                   |
| %%     | 一个%字符本身          |
| %n     | 与平台有关的行分隔符   |





```
public class Main {
    public static void main(String[] args) {
        double d = 3.1415926;
        System.out.printf("%.2f\n", d);
        System.out.printf("%.4f\n", d);
    }
}

>>>
3.14
3.1416
```



### 日期时间转换符

| 转换符 | 类型                     | 例子                         |       |
| ------ | ------------------------ | ---------------------------- | ----- |
| c      | 完成的时间和日期         | Mon Feb 09 18:05:19 PST 2019 |       |
| D      | 美国格式日期（月/日/年） | 02/09/2019                   |       |
| T      | 24小时时间               | 18:05:09                     |       |
| Y      | 4位数字年                | 2019                         |       |
| B      | 月完整拼写               | February                     |       |
| m      | 2位数字月                | 02                           |       |
| d      | 2为数字日                | 09                           |       |
| A      | 星期完整拼写             | Monday                       |       |
| H      | 2位数字小时              | 18                           |       |
| M      | 2为数字分钟              | 05                           |       |
| S      | 2为数字秒                | 19                           | 1起起 |
| Z      | 时区                     | PST                          |       |



# var关键字

有些时候，类型的名字太长，写起来比较麻烦。例如：

```
StringBuilder sb = new StringBuilder();
```

这个时候，如果想省略变量类型，可以使用`var`关键字：

```
var sb = new StringBuilder();
```

编译器会根据赋值语句自动推断出变量`sb`的类型是`StringBuilder`。对编译器来说，语句：

```
var sb = new StringBuilder();
```

实际上会自动变成：

```
StringBuilder sb = new StringBuilder();
```

因此，使用`var`定义变量，仅仅是少写了变量类型而已。





# 命令行参数

Java程序的入口是`main`方法，而`main`方法可以接受一个命令行参数，它是一个`String[]`数组。

命令行参数由JVM接收用户输入并传给`main`方法



## 位置参数

我们可以利用接收到的命令行参数，根据不同的参数执行不同的代码。例如，实现一个`-version`参数，打印程序版本号

```
public class Main {
    public static void main(String[] args) {
        for (String arg: args) {
            if ("-version".equals(arg)) {
                System.out.println("v 1.0");
                break;
            }
        }
    }
}

>>>
javac Main.java
>>>
java Main -version
>>>
v 1.0
```





# Terminology



```
  ┌─    ┌──────────────────────────────────┐
  │     │     Compiler, debugger, etc.     │
  │     └──────────────────────────────────┘
 JDK ┌─ ┌──────────────────────────────────┐
  │  │  │                                  │
  │ JRE │      JVM + Runtime Library       │
  │  │  │                                  │
  └─ └─ └──────────────────────────────────┘
        ┌───────┐┌───────┐┌───────┐┌───────┐
        │Windows││ Linux ││ macOS ││others │
        └───────┘└───────┘└───────┘└───────┘
```



## JDK

Java Development Kit
编写Java程序的软件

如果只有Java源码，要编译成Java字节码，就需要JDK，因为JDK除了包含JRE，还提供了编译器、调试器等开发工具





## JRE

Java Runtime Environment 

运行Java程序用户使用的软件， 包含虚拟机但不包含编译器

为专门不需要编译器的用户使用



## SE

Standard Edition 

用于桌面或简单服务器应用的Java平台



```
┌───────────────────────────┐
│Java EE                    │
│    ┌────────────────────┐ │
│    │Java SE             │ │
│    │    ┌─────────────┐ │ │
│    │    │   Java ME   │ │ │
│    │    └─────────────┘ │ │
│    └────────────────────┘ │
└───────────────────────────┘
```



## EE

Enterprise Edition

用于服务器应用的Java平台



## JSR

Java Specification Request

为了保证Java语言的规范性，SUN公司搞了一个JSR规范，凡是想给Java平台加一个功能，比如说访问数据库的功能，大家要先创建一个JSR规范，定义好接口，这样，各个数据库厂商都按照规范写出Java驱动程序，开发者就不用担心自己写的数据库代码在MySQL上能跑，却不能跑在PostgreSQL上。



## JCP

Java Community Process



## RI

Reference Implementation

比如有人提议要搞一个基于Java开发的消息服务器，这个提议很好啊，但是光有提议还不行，得贴出真正能跑的代码，这就是RI。

通常来说，RI只是一个“能跑”的正确的代码，它不追求速度，所以，如果真正要选择一个Java的消息服务器，一般是没人用RI的，大家都会选择一个有竞争力的商用或开源产品。



## TCK

Technology Compatibility Kit

如果有其他人也想开发这样一个消息服务器，如何保证这些消息服务器对开发者来说接口、功能都是相同的？所以还得提供TCK。



## src.zip

包含所有公共类库的源代码



## JAVA_HOME 的bin目录下

### java

这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；



### javac

这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）；



### jar

用于把一组`.class`文件打包成一个`.jar`文件，便于发布；



### javadoc

用于从Java源码中自动提取注释并生成文档；



### jdb

java调试器，用于开发阶段的运行调试。





# 版本

Java SE 6， 7， 8 对应为内部版本号 1.6.0， 1.7.0， 1.8.0

Java SE 8u31表示 Java SE 8 的第31次更新，内部版本号为1.8.0_31， 更新不需要安装在前一个版本之上，其会包含整个JDK的最新版本（这里并不表示所有更新都会公开发布）




