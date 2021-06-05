---
title: "errors"
date: 2021-05-30 21:11
---





[toc]





# try catch

try块中包含可能出错的代码，catch块用于处理try块发生的异常



## java

```
public class my_exception {
    public static void main(String[] args) {
        try {
            int i = 0;
            int j = 10;
            int a = j/i;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println("exception finally");
        }
    }
}

>>>
java.lang.ArithmeticException: / by zero
	at my_exception.main(my_exception.java:6)
exception finally
```





## scala

```
object scala_function {
    def main(args: Array[String]): Unit = {
        try {
            var r = 10/0
        } catch {
            case ex: ArithmeticException => println("get by zero error")
            case ex: Exception => println("get rest of errors")
        } finally {
            println("try finally statement")
        }
    }
}

>>>
get rest errors
try finally statement
```













