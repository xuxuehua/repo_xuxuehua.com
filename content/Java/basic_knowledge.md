---
title: "basic_knowledge"
date: 2019-11-02 11:09
---
[TOC]




# Java

Java介于编译型语言和解释型语言之间

Java是将代码编译成一种“字节码”，它类似于抽象的CPU指令，然后，针对不同平台编写虚拟机，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果





# Installation

## mac



### JDK 8u291

without oracle login

```
https://gist.github.com/wavezhang/ba8425f24a968ec9b2a8619d7c2d86a6
```

```
https://javadl.oracle.com/webapps/download/AutoDL?BundleId=244564_d7fc238d0cbf4b0dac67be84580cfb4b
```







### adoptopenjdk8

Oracle has a poor record for making it easy to install and configure Java, but using [Homebrew](http://brew.sh/), the latest OpenJDK (Java 14) can be installed with:

```java
brew install --cask adoptopenjdk8
```

For the many use cases depending on an older version (commonly Java 8), the [AdoptOpenJDK](https://adoptopenjdk.net/) project makes it possible with an extra step.

```java
brew tap adoptopenjdk/openjdk
brew install --cask adoptopenjdk8
```

Existing users of Homebrew may encounter `Error: Cask adoptopenjdk8 exists in multiple taps` due to prior workarounds with different instructions. This can be solved by fully specifying the location with `brew install --cask adoptopenjdk/openjdk/adoptopenjdk8`.



download jdk 13

```
vim ~/.bash_profile
export JAVA_HOME=`/usr/libexec/java_home -v 13`
export PATH=$JAVA_HOME/bin:$PATH
```



## linux

```
wget https://mirrors.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz

vim /etc/profile
export JRE_HOME=/root/java_web/jdk1.8.0_201/jre
export JAVA_HOME=/root/java_web/jdk1.8.0_201
export JAVA_BIN=/root/java_web/jdk1.8.0_201/bin
PATH=$PATH:$JAVA_BIN
```



# Java基本数据类型占用的字节数

| byte   | 1    |
| ------ | ---- |
| short  | 2    |
| int    | 4    |
| long   | 8    |
| float  | 4    |
| double | 8    |
| char   | 2    |





# Hello World

```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

> 方法名是main，void是返回值，表示没有任何返回值
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



# null  

null是所有引用类型的默认值

引用类型的变量可以指向一个空值`null`，它表示不存在，即该变量不指向任何对象

```
String s1 = null; // s1是null
String s2; // 没有赋初值值，s2也是null
String s3 = s1; // s3也是null
String s4 = ""; // s4指向空字符串，不是null
```



## 注意点

任何含有null值的包装类在Java拆箱生成基本数据类型时候都会抛出一个空指针异常

# void vs Void 

void不是函数，是方法的修饰符，void的意思是该方法没有返回值，意思就是方法只会运行方法中的语句，但是不返回任何东西。

 java.lang.Void是一种类型。例如给Void引用赋值null。通过Void类的源代码可以看到，Void类型不可以继承与实例化。

```
public final class Main {

    public void do1() {
        return; //返回void，return可写可不写
    }

    public Void do2() {
        return null; //此处必须返回null 返回其余类型都不好使
    }
}
```



```
public final class Main {

    public static void main(String[] args) {
        System.out.println(Void.class); //class java.lang.Void
        System.out.println(void.class); //void
        //类似于下面的
        System.out.println(Integer.class); //class java.lang.Integer
        System.out.println(int.class); //int
    }
}
```







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







# 基本数据类型

Java定义了以下几种基本数据类型：

- 整数类型：byte，short，int，long
- 浮点数类型：float，double
- 字符类型：char
- 布尔类型：boolean

不同的数据类型占用的字节数不一样。我们看一下Java基本数据类型占用的字节数：

```
       ┌───┐
  byte │   │
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

>  `byte`恰好就是一个字节，而`long`和`double`需要8个字节。





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

这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码



### javac

这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）



### jar

用于把一组`.class`文件打包成一个`.jar`文件，便于发布



### javadoc

用于从Java源码中自动提取注释并生成文档



### jdb

java调试器，用于开发阶段的运行调试





# 版本

Java SE 6， 7， 8 对应为内部版本号 1.6.0， 1.7.0， 1.8.0

Java SE 8u31表示 Java SE 8 的第31次更新，内部版本号为1.8.0_31， 更新不需要安装在前一个版本之上，其会包含整个JDK的最新版本（这里并不表示所有更新都会公开发布）



| 1995   | 1.0       |
| ------ | --------- |
| 1998   | 1.2       |
| 2000   | 1.3       |
| 2002   | 1.4       |
| 2004   | 1.5 / 5.0 |
| 2005   | 1.6 / 6.0 |
| 2011   | 1.7 / 7.0 |
| 2014   | 1.8 / 8.0 |
| 2017/9 | 1.9 / 9.0 |
| 2018/3 | 10        |
| 2018/9 | 11        |
| 2019/3 | 12        |
| 2019/9 | 13        |





# Appendix

https://gist.github.com/wavezhang/ba8425f24a968ec9b2a8619d7c2d86a6

