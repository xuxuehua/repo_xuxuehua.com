---
title: "cprofile 性能分析"
date: 2019-09-29 07:07
---
[TOC]



# cProfile

所谓的profile，是指对代码的每个部分进行动态的分析，比如准确计算出每个模块消耗的时间等。这样
你就可以知道程序的瓶颈所在，从而对其进行修正或优化。当然，这并不需要你花费特别大的力气，在
Python中，这些需求用cProfile就可以实现。



[https://docs.python.org/3.7/library/profile.html](https://docs.python.org/3.7/library/profile.html)





## Example

斐波那契数列

```
import cProfile

def fib(n):
    if n == 0:
        return 0 
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)


def fib_seq(n):
    res = []
    if n > 0:
        res.extend(fib_seq(n-1))
    res.append(fib(n))
    return res

cProfile.run('fib_seq(30)')

>>>
         7049218 function calls (96 primitive calls) in 1.546 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
(gevent_3.7.0) rxus-MacBook-Pro:my_test_file rxu$ python my_cprofile.py 
         7049218 function calls (96 primitive calls) in 1.554 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    1.554    1.554 <string>:1(<module>)
     31/1    0.000    0.000    1.554    1.554 my_cprofile.py:12(fib_seq)
7049123/31    1.554    0.000    1.554    0.050 my_cprofile.py:3(fib)
        1    0.000    0.000    1.554    1.554 {built-in method builtins.exec}
       31    0.000    0.000    0.000    0.000 {method 'append' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' 
objects}
       30    0.000    0.000    0.000    0.000 {method 'extend' of 'list' objects}
```

> ncalls，是指相应代码/函数被调用的次数; 
>
> tottime，是指对应代码/函数总共执行所需要的时间 (注意，并不包括它调用的其他代码/函数的执行时间); 
>
> tottimepercall，就是上述两者相除的结果，也就是tottime / ncalls; cumtime，则是指对应代码/函数总共执行所需要的时间，这里包括了它调用的其他代码/函数的执行时 
>
> 间;
>  cumtime percall，则是cumtime和ncalls相除的平均结果。 



或者

```
python3 -m cProfile xxx.py
```





### 改进后

```
import cProfile

def memorize(f):
    memo = {}
    def helper(x):
        if x not in memo:
            memo[x] = f(x)
        return memo[x]
    return helper

@memorize
def fib(n):
    if n == 0:
        return 0 
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)


def fib_seq(n):
    res = []
    if n > 0:
        res.extend(fib_seq(n-1))
    res.append(fib(n))
    return res

cProfile.run('fib_seq(30)')

>>>
         215 function calls (127 primitive calls) in 0.000 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.000    0.000 <string>:1(<module>)
       31    0.000    0.000    0.000    0.000 my_cprofile.py:11(fib)
     31/1    0.000    0.000    0.000    0.000 my_cprofile.py:21(fib_seq)
    89/31    0.000    0.000    0.000    0.000 my_cprofile.py:5(helper)
        1    0.000    0.000    0.000    0.000 {built-in method builtins.exec}
       31    0.000    0.000    0.000    0.000 {method 'append' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
       30    0.000    0.000    0.000    0.000 {method 'extend' of 'list' objects}

```

