---
title: "function 函数"
date: 2020-01-24 02:49
---
[toc]



# 函数

```
    public static void sum(int a, int b) {
        int i;
        int sum = 0;
        for (i=a; i<=b; i++) {
            sum += i;
        }
        System.out.println("sum="+sum);
    }
```





## 本地变量

需要定义在块内，即函数内，或语句块内

程序在运行在块之前，变量不存在，离开块之后，变量就消失



## return

函数可以没有return

```
    public static int max (int a, int b) {
        int ret;
        if (a>b) {
            ret = a;
        }
        else {
            ret = b;
        }
        return ret;
    }
```



## 参数类型不匹配



### 预期大于调用时

编译器可以自动降类型转换好



### 预期小于调用时

需要进行强制类型转换

```
(int)5.0
```



### 预期不同于调用时

无法继续执行