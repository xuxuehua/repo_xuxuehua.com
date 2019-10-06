---

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



