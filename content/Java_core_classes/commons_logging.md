---
title: "commons_logging"
date: 2019-10-24 23:44
---
[TOC]



# Commons Logging

和Java标准库提供的日志不同，Commons Logging是一个第三方日志库，它是由Apache创建的日志模块。

Commons Logging的特色是，它可以挂接不同的日志系统，并通过配置文件指定挂接的日志系统。默认情况下，Commons Loggin自动搜索并使用Log4j（Log4j是另一个流行的日志系统），如果没有找到Log4j，再使用JDK Logging。



## 安装

下载解压，找到`commons-logging-1.2.jar`这个文件

http://mirrors.tuna.tsinghua.edu.cn/apache//commons/logging/binaries/commons-logging-1.2-bin.tar.gz



再把commons-logging-1.2.jar 与Main.java 放置于同一个目录下

```
javac -cp commons-logging-1.2.jar Main.java
```

编译成功，那么当前目录下就会多出一个`Main.class`文件

```
java -cp .:commons-logging-1.2.jar Main

>>>
Oct 25, 2019 7:04:21 AM Main main
INFO: start...
Oct 25, 2019 7:04:21 AM Main main
WARNING: end...
```





## 日志级别

默认级别是`INFO`

- FATAL
- ERROR
- WARNING
- INFO
- DEBUG
- TRACE



## 引用



### 静态方法

```
public class Main {
    static final Log log = LogFactory.getLog(Main.class);

    static void foo() {
        log.info("foo");
    }
}
```



### 实例方法

```
public class Person {
    protected final Log log = LogFactory.getLog(getClass());

    void foo() {
        log.info("foo");
    }
}
```

> 实例变量log的获取方式是`LogFactory.getLog(getClass())`，虽然也可以用`LogFactory.getLog(Person.class)`，但是前一种方式有个非常大的好处，就是子类可以直接使用该`log`



#### 子类使用父类

由于Java类的动态特性，子类获取的`log`字段实际上相当于`LogFactory.getLog(Student.class)`，但却是从父类继承而来，并且无需改动代码

```
public class Person {
    protected final Log log = LogFactory.getLog(getClass());

    void foo() {
        log.info("foo");
    }
}

public class Student extends Person {
    void bar() {
        log.info("bar");
    }
}
```



## 重载方法

Commons Logging的日志方法，例如`info()`，除了标准的`info(String)`外，还提供了一个非常有用的重载方法：`info(String, Throwable)`，这使得记录异常更加简单

```
try {
    ...
} catch (Exception e) {
    log.error("got exception!", e);
}
```











