---
title: "float 浮点"
date: 2019-10-06 11:32
collection: 基本变量类型
---
[TOC]



# float 浮点 (很少使用)

浮点类型的数就是小数， 即小数点是浮动的

浮点数运算和整数运算相比，只能进行加减乘除这些数值计算，不能做位运算和移位运算

4个字节存储

float类型可最大表示3.4x10^38

float类型的精度很难满足需求，实际上，只有很少的情况适合float 类型



# double

8个字节存储

double类型可最大表示1.79x10^308

double表示这种类型的数值精度是float 类型的两倍，绝大数应用程序都采用double类型





# 定义

```
float f1 = 3.14f;
float f2 = 3.14e38f; // 科学计数法表示的3.14x10^38
double d = 1.79e308;
double d2 = -1.79e308;
double d3 = 4.9e-324; // 科学计数法表示的4.9x10^-324
```



## 特点

浮点数`0.1`在计算机中就无法精确表示，因为十进制的`0.1`换算成二进制是一个无限循环小数，很显然，无论使用`float`还是`double`，都只能存储一个`0.1`的近似值

因为浮点数常常无法精确表示，因此，浮点数运算会产生误差

```
public class Main {
    public static void main(String[] args) {
        double x = 1.0 / 10;
        double y = 1 - 9.0 / 10 ;
        System.out.println(x);
        System.out.println(y);
    }
}

>>>
0.1
0.09999999999999998
```



### 浮点数比较

由于浮点数存在运算误差，所以比较两个浮点数是否相等常常会出现错误的结果。正确的比较方法是判断两个浮点数之差的绝对值是否小于一个很小的数

```
// 比较x和y是否相等，先计算其差的绝对值:
double r = Math.abs(x - y);
// 再判断绝对值是否足够小:
if (r < 0.00001) {
    // 可以认为相等
} else {
    // 不相等
}
```





# 类型转换



## int to float 

```
public class Main {
    public static void main(String[] args) {
        int n = 5;
        double d = 1.2 + 24.0 / n;
        System.out.println(d);
    }
}

>>>
6.0
```

> 需要特别注意，在一个复杂的四则运算中，两个整数的运算不会出现自动提升的情况
>
> ```
> double d = 1.2 + 24 / 5; // 5.2
> ```



## float to int

可以将浮点数强制转型为整数。在转型时，浮点数的小数部分会被丢掉。如果转型后超过了整型能表示的最大范围，将返回整型的最大值。

```
int n1 = (int) 12.3; // 12
int n2 = (int) 12.7; // 12
int n2 = (int) -12.7; // -12
int n3 = (int) (12.7 + 0.5); // 13
int n4 = (int) 1.2e20; // 2147483647
```





# 误差

```
public class Main {
    public static void main(String[] args) {
        System.out.println(1.2-1.1);
    }
}

>>>
0.09999999999999987
```



# 溢出

整数运算在除数为`0`时会报错，而浮点数运算在除数为`0`时，不会报错，但会返回几个特殊的浮点数值

- `NaN`表示Not a Number
- `Infinity`表示正无穷大
- `-Infinity`表示负无穷大



## 检测

使用Double.isNan 方法判断

``` 
if (Double.isNan(x))  // check whether x is "not a number"
```



