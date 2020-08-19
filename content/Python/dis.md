---
title: "dis"
date: 2020-08-16 09:04
---
[toc]



# python字节码

 Python 代码先被编译为字节码后，再由Python虚拟机来执行字节码， Python的字节码是一种类似汇编指令的中间语言， 一个Python语句会对应若干字节码指令，虚拟机一条一条执行字节码指令， 从而完成程序执行。 Python dis 模块支持对Python代码进行反汇编， 生成字节码指令。

# dis

Dis模块来对代码进行性能剖析





```
import dis


def test1(a):
    if 0 < a and a < 1:
        return 1
    return 0


def test2(a):
    if 0 < a < 1:
        return 1
    return 0


dis.dis(test1)
print('-'*20)
dis.dis(test2)

>>>
  5           0 LOAD_CONST               1 (0)
              2 LOAD_FAST                0 (a)
              4 COMPARE_OP               0 (<)
              6 POP_JUMP_IF_FALSE       20
              8 LOAD_FAST                0 (a)
             10 LOAD_CONST               2 (1)
             12 COMPARE_OP               0 (<)
             14 POP_JUMP_IF_FALSE       20

  6          16 LOAD_CONST               2 (1)
             18 RETURN_VALUE

  7     >>   20 LOAD_CONST               1 (0)
             22 RETURN_VALUE
--------------------
 11           0 LOAD_CONST               1 (0)
              2 LOAD_FAST                0 (a)
              4 DUP_TOP
              6 ROT_THREE
              8 COMPARE_OP               0 (<)
             10 POP_JUMP_IF_FALSE       20
             12 LOAD_CONST               2 (1)
             14 COMPARE_OP               0 (<)
             16 POP_JUMP_IF_FALSE       28
             18 JUMP_FORWARD             4 (to 24)
        >>   20 POP_TOP
             22 JUMP_FORWARD             4 (to 28)

 12     >>   24 LOAD_CONST               2 (1)
             26 RETURN_VALUE

 13     >>   28 LOAD_CONST               1 (0)
             30 RETURN_VALUE
```

> 第一列：对应的源代码行数。
> 第二列：对应的内存字节码的索引位置。
> 第三列：内部机器代码的操作。
> 第四列：指令参数。
> 第五列：实际参数。
> `>>`： 表示跳转目标
>
> 第二个函数的字节码索引最大到了30，而第一个函数的字节码索引最大仅到了22，因此，第一个函数耗得内存比第二个函数少