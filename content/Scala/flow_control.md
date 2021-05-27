---
title: "flow_control"
date: 2021-05-19 21:45
---
[toc]





# if

```
object scala_if_statement {
    def main(args: Array[String]): Unit = {
        val name: String = "Rick"
        val age: Int = 18
        val gender: String = "Male"
        val rxu: Boolean = true;
        if (rxu) {
            println("rxu is true")
        }
				// In general, use equals to compare Strings 
        if ("Male".equals(gender)) {
            println(s"gender=$gender")
        }

        if (name == "Rick") {
            println("name is correct")
        } else {
            println("name is incorrect")
        }

        if (age < 10) {
            println("Too young")
        } else if (age < 20) {
            println("It's OK")
        } else {
            println("Looks well")
        }
    }
}

```

> ```
> rxu is true
> gender=Male
> name is correct
> It's OK
> ```
>
> 





# for

默认情况下，for循环到返回值是 `()`





## to

```
object scala_flow_control {
    def main(args: Array[String]): Unit = {
        // 0 to 5 表示循环范围
        // 0.to(5) 可省略成 0 to 5
        for ( i <- 0 to 5) {
            println(s"i=${i}")
        }
    }
}

>>>
i=0
i=1
i=2
i=3
i=4
i=5
```





## until

```
object scala_flow_control {
    def main(args: Array[String]): Unit = {
        // 0 until 4 表示循环范围
        // 0.util(5) 可省略成 0 util 5
        for ( i <- 0 until 5) {
            println(s"i=${i}")
        }
    }
}

>>>
i=0
i=1
i=2
i=3
i=4
```







## Range 范围对象 （可指定step）

```
Range(start, end, step)
```



```
object scala_flow_control {
    def main(args: Array[String]): Unit = {
        for (i <- Range(0, 5, 2)) {
            println(s"i=${i}")
        }
    }
}

>>>
i=0
i=2
i=4
```





# continue （Scala 默认没有）

使用if else或是循环守卫方式实现continue

```
object scala_flow_control {
    def main(args: Array[String]): Unit = {
        for (i <- 1 to 5 if i%2 == 0) {
            print(s"i=${i}\n")
        }
        // 等价于
        for (i <- 1 to 5) {
            if (i%2 == 0) {
                print(s"i=${i}\n")
            }
        }
    }
}

>>>
i=2
i=4
i=2
i=4
```







# Break (对象方式实现)

Scala 默认没有break方法，通过Breads对象方式来实现

break方法是通过抛异常来实现, 需要通过breakable来捕获异常

```
import scala.util.control.Breaks

object scala_flow_control {
    def main(args: Array[String]): Unit = {
        Breaks.breakable(
            for (i <- 1 to 10) {
                if (i == 5) {
                    Breaks.break()
                }
                println(s"i=${i}")
            }
        )
        println("loop operation has done")
    }
}

>>>
i=1
i=2
i=3
i=4
loop operation has done
```





# while (不推荐)

因为while中没有返回值， 所以当要用该语句来计算并返回结果时，就不可避免的要使用变量

而变量需要声明在while循环外部，这样外部就对内部变量造成影响，因此不推荐使用，用for循环替代





# do while

和while情况一样，因为do while中没有返回值，所以用该语句来计算并返回结果时，不可避免需要使用变量。