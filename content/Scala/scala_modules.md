---
title: "scala_modules"
date: 2021-06-02 22:56
---





[toc]



# packages



Scala 中的包声明方式，默认和Java是一致的

对比Java，scala中的package可以在同一源码文件中多次声明

Scala中，源码中的类所在的位置不需要和包路径相同



Scala中的所有语法都可以进行嵌套，package也可以

```
package test1 {
    package test2 {
        object scala_package {
            def main(args: Array[String]): Unit = {
                println("Rick")
            }
        }
    }
}

>>>
Rick
```



packages 可以使用小括号, 小括号内声明的类在这个包中，之外声明的类不在这个包中



scala中可以声明父包和子包，父包中的类，子类可以直接访问，不需要用引用

scala 中package可以声明类，但是无法声明变量和函数方法





# import （导入类）

用于导入类



## `_` 通配符

import可以导入一个包中的所有类，采用下划线代替星号



## `{}` 可包含多个类





## `_root_` 绝对路径

需要从最开始的包中查找类，添加`_root_` 开头获取绝对路径









