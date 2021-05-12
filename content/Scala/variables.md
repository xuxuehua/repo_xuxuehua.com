---
title: "variables"
date: 2021-04-27 23:49
---
[toc]



# 变量

Scala与Java有相同的数据类型，在Scala中数据类型都是对象，也就是Scala中没有javashooing的原生（基本）类型







## var/val 声明变量

var声明的变量，变量的值是可以修改的

var修饰的对象引用可以改变，val修饰的则不可以改变，但对象的状态（值）却是可以改变的

```
object print_string {
    def main(args: Array[String]): Unit = {
        val dog = new Dog()
        dog.name = "wangwang"
        println(dog.name)
    }
}

class Dog {
    var name: String = ""
}

```

> ```
> wangwang
> ```
>
> 



val声明的变量，变量的值是不可以修改的, 即声明之后，内存地址不变。

val修饰的对象属性在编译之后，等同于加上了final

```
object pint_string {
    // 在方法外部声明的变量，如果采用val关键字，等同于使用final修饰
    val gender: String = "Male"
    def main(args: Array[String]): Unit = {
        val name: String = "Rick"
        // 可以省略类型指令
        val age = 18
        var score: Double = 99.999
        var b: Boolean = true
        println(s"name=${name}, age=${age}, gender=${gender}")
    }
}

```

> ```
> name=Rick, age=18, gender=Male
> ```
>
> 



## 特点

scala是完全面向对象的语言，没有基本的数据类型

所以scala中数字也是对象，可以调用方法



```
object ScalaStringType {
    def main(args: Array[String]): Unit = {
        val age: int = 20
        // byte, short, int, long, float, double, boolean, char
        val b: Byte = 10
        val s: Short = 10
        val i: Int = 10
        val lon: Long = 10
        val f: Float = 10.0f
        val d: Double = 10
        val bln: Boolean = true
        val c: Char = 'c'
        val in: Int = 10
    }
}
```



# 类型划分

无论是AnyVal还是AnyRef，都是对象， 相互之间无法转换



| 数据类型 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| Byte     | 8位有符号补码整数。数值区间为 -128 到 127                    |
| Short    | 16位有符号补码整数。数值区间为 -32768 到 32767               |
| Int      | 32位有符号补码整数。数值区间为 -2147483648 到 2147483647, 默认类型 |
| Long     | 64位有符号补码整数。数值区间为 -9223372036854775808 到 9223372036854775807 |
| Float    | 32 位, IEEE 754 标准的单精度浮点数， 默认类型                |
| Double   | 64 位 IEEE 754 标准的双精度浮点数                            |
| Char     | 16位无符号Unicode字符, 区间值为 U+0000 到 U+FFFF             |
| String   | 字符序列                                                     |
| Boolean  | true或false                                                  |
| Unit     | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null     | null 或空引用， 该类型只有一个实例值null                     |
| Nothing  | Nothing类型在Scala的类层级的最底端；它是任何其他类型的子类型。当一个函数没有正常的返回值，可以用Nothing来指定返回类型 |
| Any      | Any是所有其他类的超类                                        |
| AnyRef   | AnyRef类是Scala里所有引用类(reference class)的基类           |



## AnyVal 值类型

```
Double
Float
Long
Int
Short
Byte
Unit
StringOps
Char
Boolean
```



## AnyRef 引用类型

```
Scala collections
Other Scala classes
All java classes
Null
Nothing
```



### Nothing

```

```















