---
title: "basic_knowledge"
date: 2021-04-24 10:45
---
[toc]



# Scala

Scalable Language 简写，是一门多范式的编程语言

以Java虚拟机为目标运行环境，并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语言

scala源码会被编译成为Java字节码(`.class`) ，然后运行于JVM之上，并可以调用现有的Java类库，实现两种语言无缝对接





# Installation



## mac intellij

To check, open the terminal and type:

```
java -version  (Make sure you have version 1.8 or 11.)
```

*(If you don't have it installed, download Java from [Oracle Java 8](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html), [Oracle Java 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html), or [AdoptOpenJDK 8/11](https://adoptopenjdk.net/). Refer [JDK Compatibility](https://docs.scala-lang.org/overviews/jdk-compatibility/overview.html) for Scala/Java compatiblity detail.*



∆Install scala on IntelliJ

**Creating the Project**

1. Open up IntelliJ and click **File** => **New** => **Project**
2. On the left panel, select Scala. On the right panel, select IDEA.
3. Name the project **HelloWorld**
4. Assuming this is your first time creating a Scala project with IntelliJ, you’ll need to install a Scala SDK. To the right of the Scala SDK field, click the **Create** button.
5. Select the highest version number (e.g. 2.13.5) and click **Download**. This might take a few minutes but subsequent projects can use the same SDK.
6. Once the SDK is created and you’re back to the “New Project” window click **Finish**.





# Hello World

```
// 伴生对象用object声明

object Hello {
    def main(args: Array[String]): Unit = {
        println("Hello World")
    }
}
```

> ```
> >>>
> Hello World
> ```
>
> 



scalac 编译

```
rxu@localhost ~/IdeaProjects/src $ ls
hello.scala
rxu@localhost ~/IdeaProjects/src $ scalac hello.scala 

# 含$表示内部类，用来模拟java中的静态语法，称为伴生对象，其内容可以对象名访问
rxu@localhost ~/IdeaProjects/src $ ls
Hello$.class    Hello.class     hello.scala

# 这里的参数是class名
rxu@localhost ~/IdeaProjects/src $ scala Hello
Hello World

# 可以直接运行，其在内存中编译完成后再执行
rxu@localhost ~/IdeaProjects/src $ scala hello.scala 
Hello World
```



## 对比java特点

scala中没有public关键字，默认所有的访问权限都是公共的

scala中没有void关键字，采用特殊的对象来模拟，即Unit

scala使用def关键字声明方法

Java 的参数，是先写类型，再写参数名。scala却相反，参数名放前，然后再放类型

Java中的声明和方法体是直接连接括号，但scala中间需要通过等号连接

scala中将方法的返回值类型，放置在方法声明的后面使用冒号连接

对比Java，scala可以不用在行结尾加分号。尽量一行就写一条语句







## decompiler



scala 源码中包含了main方法，在编译之后自动形成了

```
public void main
```



并且会生成两个字节码文件，静态main方法执行另外一个字节码文件中的成员main方法

scala是完全面向对象的语言，那么没有静态的语法，只能通过模拟生成静态方法

所以，编译的时候，将当前类生成一个特殊的类，即带有$的类，然后创建对象来调用这个对象的main方法



```
//decompiled from Hello.class
import scala.reflect.ScalaSignature;

@ScalaSignature(
   bytes = "\u0006\u0001\u0015:Q!\u0001\u0002\t\u0002\u0015\tQ\u0001S3mY>T\u0011aA\u0001\by\u0015l\u0007\u000f^=?\u0007\u0001\u0001\"AB\u0004\u000e\u0003\t1Q\u0001\u0003\u0002\t\u0002%\u0011Q\u0001S3mY>\u001c\"a\u0002\u0006\u0011\u0005-qQ\"\u0001\u0007\u000b\u00035\tQa]2bY\u0006L!a\u0004\u0007\u0003\r\u0005s\u0017PU3g\u0011\u0015\tr\u0001\"\u0001\u0013\u0003\u0019a\u0014N\\5u}Q\tQ\u0001C\u0003\u0015\u000f\u0011\u0005Q#\u0001\u0003nC&tGC\u0001\f\u001a!\tYq#\u0003\u0002\u0019\u0019\t!QK\\5u\u0011\u0015Q2\u00031\u0001\u001c\u0003\u0011\t'oZ:\u0011\u0007-ab$\u0003\u0002\u001e\u0019\t)\u0011I\u001d:bsB\u0011qD\t\b\u0003\u0017\u0001J!!\t\u0007\u0002\rA\u0013X\rZ3g\u0013\t\u0019CE\u0001\u0004TiJLgn\u001a\u0006\u0003C1\u0001"
)
public final class Hello {
   public static void main(String[] var0) {
      Hello$.MODULE$.main(var0);
   }
}

//decompiled from Hello$.class
import scala.Predef.;

public final class Hello$ {
   public static final Hello$ MODULE$;

   static {
      new Hello$();
   }

   public void main(String[] args) {
      .MODULE$.println("Hello World");
   }

   private Hello$() {
      MODULE$ = this;
   }
}
```





## 执行流程

```
.scala 文件 -> scalac 编译 -> 生成 .class 的字节码文件 -> scala -> 得到结果
或者
.scala 文件 -> scala 编译和运行 -> 得到结果
```





# print 输出



## +号连接

```
object pint_string {
    def main(args: Array[String]): Unit = {
        val name = "Rick"
        val age = 18
        println("name=" + name + ", age=" + age)
    }
}
```

> ```
> name=Rick, age=18
> ```
>
> 



## %号传值

```
object pint_string {
    def main(args: Array[String]): Unit = {
        val name = "Rick"
        val age = 18
        printf("name=%s, age=%d \n", name, age)
    }
}
```

> ```
> name=Rick, age=18 
> ```
>
> 





## $号引用 （推荐）

```
object pint_string {
    def main(args: Array[String]): Unit = {
        val name = "Rick"
        val age = 18
        println(s"name=$name, age=$age")
    }
}

```

> ```
> name=Rick, age=18
> ```
>





```
object pint_string {
    def main(args: Array[String]): Unit = {
        val name = "Rick"
        val age = 18
        println(f"name=${name}, age=${age}%.2f")
    }
}
```

> ```
> name=Rick, age=18.00
> ```
>
> 



```
object pint_string {
    def main(args: Array[String]): Unit = {
        val name = "Rick"
        val age = 18
        println(raw"name=${name}, age=${age}%.2f \n")
    }
}
```

> ```
> name=Rick, age=18%.2f \n
> ```
>
> 







# 注释



```
object pint_string {
    def main(args: Array[String]): Unit = {
//        val name = "Rick"
        
/*        val age = 18
        println(raw"name=${name}, age=${age}%.2f \n")
 */
        
/**
 * doc string section
 */
    }
}
```





# Appendix

https://www.scala-lang.org/download/