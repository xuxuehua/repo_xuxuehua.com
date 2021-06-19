---
title: "class"
date: 2021-05-30 21:29
---





[toc]





# class



```
object scala_object {
    def main(args: Array[String]): Unit = {
        val user: User = new User()
        user.username = "Rick"
        println(user.username)
    }
}

class User {
    // 使用下划线进行默认初始化
    var username: String = _
    var age: Int = _
    def login(): Boolean = {
        true
    }
}

>>>
Rick
```



## attribute

scala中给类声明属性，默认为私有的，但是底层提供了公共的setter和getter方法



```
object scala_object {
    def main(args: Array[String]): Unit = {
        val user: User = new User()
        // using default setter
        user.username = "Rick"
        
        // using default getter
        println(user.username)
    }
}

class User {
    // 使用下划线进行默认初始化
    var username: String = _
}

>>>
Rick 
Sam
```





### private 修饰符

设置private修饰符，属性无法在外部访问，因为底层生成的setter和getter方法都是私有的





### final 修饰符

如果声明的属性使用val，那么那么属性是私有的，并且使用final修饰符，底层只提供getter方法，没有setter方法

```
object scala_object {
    def main(args: Array[String]): Unit = {
        val user: User = new User()
        // using default setter
        user.username = "Rick"

        // using default getter
        println(user.username)
    }
}

class User {
    // 使用下划线进行默认初始化
    var username: String = _
    private var age: Int = _
    final val gender: String = "Male"
}

>>>
Rick
```







# Privileges

Scala中有四种访问权限

```
package p1 {
   package p2 {
      class userP2 {
         var username = "Rick"
         private var password = "rick_pass"
         protected var email = "rickxu1989@gmail.com"
         private[p2] var address = "China"
      }
   }

   package p3 {
      import p1.p2.userP2

      class P3 extends userP2 {
          def p3Test(): Unit = {
             val user = new userP2
              user.username = ""
              user.email = ""
          }
       }
   }
}
```



## public （默认访问权限）

默认访问权限，没有关键字



## protected

只能子类访问，同包不能访问



## default （package）

包访问权限需要特殊的语法规则

```
private[包名称]
```









## private

私有访问权限，只能访问当前类
