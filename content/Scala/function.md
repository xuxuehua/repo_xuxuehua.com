---
title: "function"
date: 2021-05-22 15:43
---
[toc]







# 函数式编程

关心的是问题的解决方案（封装功能），重点在于函数功能的入参和出参

Scala是完全面向函数式编程语言，即函数在Scala中 可以做任何事情





# 函数

Java中的方法和Scala中的函数都可以进行功能封装，但是方法必须和类型进行绑定，但是作为函数就不需要

```

object scala_function {
    def main(args: Array[String]): Unit = {
        my_test("Rick")
    }

    def my_test(s: String): Unit ={
        println(s"s=${s}")
    }

}

>>>
s=Rick
```





## 位置参数



```
object scala_function {
    def main(args: Array[String]): Unit = {
        // no parameters
        def test1(): Unit = {
            println("test1")
        }
        test1()

        // position parameter without return value
        def test2(s: String): Unit = {
            println(s"s=${s}")
        }
        test2("Rick")

        // position parameter with return value
        def test3(s: String): String = {
            return s+" Xu"
        }
        val ret_value: String = test3("Rick")
        println(s"ret_value=${ret_value}")

    }
}

>>>
test1
s=Rick
ret_value=Rick Xu
```



## 可变参数

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def test1(name:  String*): Unit = {
            println(s"name=${name}")
        }

        test1("Rick", "Michelle", "Sam")
    }
}

>>>
name=WrappedArray(Rick, Michelle, Sam)
```



## 关键字参数

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def test1(name: String, family: String="Xu"): Unit = {
            println(s"name=${name}, family=${family}")
        }

        test1(name="Rick")
    }
}

>>>
name=Rick, family=Xu
```









## Unit （明确该定义无返回值）

```
object scala_function {
    def main(args: Array[String]): Unit = {
        // no parameters
        def test1(): Unit = {
            return "test1"
        }
        test1()
    }
}
```



## 简化书写



### 省略return关键字

如果将函数体的最后一行代码进行返回，那么return关键字可以省略

```
object scala_function {
    def main(args: Array[String]): Unit = {
        // no parameters
        def test1(): String = {
            "test1"
        }
        println(test1())
    }
}

>>>
test1
```



### 省略类型

如果可以根据最后一行代码推断类型，那么返回值类型也可以省略

```
object scala_function {
    def main(args: Array[String]): Unit = {
        // no parameters
        def test1() = {
            "test1"
        }
        println(test1())
    }
}

>>>
test1
```



### 省略小括号

如果函数声明没有参数列表，小括号可以省略

如果小括号省略，访问函数的时候就不能增加小括号

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def my_test = "Rick"
        print(s"my_test=${my_test}")
    }
}

>>>
my_test=Rick
```





### 省略等号

如果明确函数没有返回值，那么等号可以省略，省略后，编译器不会将函数题的最后一行代码作为返回值









## 默认参数

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def test1(name: String, family: String="Xu"): Unit = {
            println(s"name=${name}, family=${family}")
        }

        test1("Rick")
    }
}

>>>
name=Rick, family=Xu
```





## 函数中返回函数

在函数中直接返回函数，会有问题，需要加上下划线处理

```
object scala_function {
    def main(args: Array[String]): Unit = {

        def f0(): Unit = {println("function0")}

        def f1() = {
            f0 _
        }
        f1()()
    }
}

>>>
function0
```





## 函数闭包

改变了外部变量的生命周期， 把他包含到了逻辑的内部，形成闭环的操作，称为闭包，即内层函数引用到了外层函数的自由变量

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def f1(i: Int)= {

            def f2(j: Int): Int = {
                i * j
            }
            f2 _ 
        }

        f1(2)(3)

    }
}

```



### Currying 柯里化 （简化函数）

柯里化是由闭包实现的

```
object scala_function {
    def main(args: Array[String]): Unit = {
        def f1(i: Int)(j: Int): Int = {
            i*j
        }
        
        println(f1(2)(3))
    }
}

>>>
6
```





# 匿名函数



## 主动执行 `()->{}` 

```
object scala_function {
    def main(args: Array[String]): Unit = {
        () -> {println("Rick")}
    }
}

>>>
Rick
```





## 调用才执行 `()=>{}`  (常用)

将函数作为参数传递给另一个函数，需要采用特殊的声明方式

```
参数列表 => 返回值类型
() => Unit
```



```
object scala_function {
    def main(args: Array[String]): Unit = {
        def f1(f: () => Int): Int = {
            f() + 10
        }

        def f2(): Int = {
            5
        }

        println(f1(f2))

    }
}

>>>
15
```





通过匿名函数实现，函数作为参数传递给另外一个函数

```
object scala_function {
    def main(args: Array[String]): Unit = {

        def f7(f: (Int)=> Unit): Unit = {
            f(100)
        }

        f7((i: Int)=>{println(i)})
        f7((i)=>{println(i)})
        f7((i)=>println(i))
        f7(println(_))
        f7(println)

        def f8(f: (Int, Int)=> Int) = {
            f(10, 10)
        }

        println(f8((x: Int, y: Int)=>{x+y}))
        println(f8((x, y)=> {x+y}))
        println(f8((x, y)=> x+y))
        println(f8(_+_))

    }
}

>>>
100
100
100
100
100
20
20
20
20
```







