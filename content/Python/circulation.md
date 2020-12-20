---
title: "circulation 循环"
date: 2018-06-29 22:32
---

[TOC]



# circulation 循环



## if

judgement 必须是一个布尔类型

```
if judgement:
	operation
```


```
if judgement:
	operation
else:
	operation
```

```
if judgement:
	operation
elif judgement:
	operation
else
	operation
```



### 真值判断

| 对象/常量  | True or False              |
| :--------- | -------------------------- |
| ""         | False                      |
| "string"   | True， 空字符串为False     |
| 0          | False， 其他数字为True     |
| \>=1       | True                       |
| \<=-1      | True                       |
| () 空元组  | False                      |
| [] 空列表  | False                      |
| {} 空字典  | False                      |
| None       | False                      |
| bool       | True为True    False为False |
| object对象 | None为False， 其他为True   |



```
In [13]: if "":
    ...:     print('yes')
    ...: else:
    ...:     print('no')
    ...:
no
```



```
In [4]: if True:
   ...:     print('Hello Python')
   ...:     
Hello Python

In [5]: if 2 > 1: 
   ...:     print('2 is greater than 1')
   ...:     
2 is greater than 1

In [6]: if 1 > 2:
   ...:     print('1 is greater than 2')
   ...: else:
   ...:     print('1 is not greater than 2')
   ...:     
1 is not greater than 2

In [7]: if 1 > 2:
   ...:     print('1 is greater than 2')
   ...: elif 2 > 1:
   ...:     print('1 is not greater than 2')
   ...: else:
   ...:     print('1 is equal to 2')
   ...:     
1 is not greater than 2
```





## while 

judgement 为True 进入循环体

循环正常执行结束，执行else子句

使用break 终止，else子句不会执行

```
while judgement:
	operation
else:
    operation
```

```
In [8]: n = 1

In [9]: while n <= 10:
   ...:     print(n)
   ...:     n += 1
   ...:     
1
2
3
4
5
6
7
8
9
10
```





## for

```
for i in range(10):
	print(i)
>>>
0
1
2
3
4
5
6
7
8
9
```



```
In [72]: items = {'a': 1, 'b':2, 'c': 3}                                                                                                                                     

In [73]: for i in items: 
    ...:     print(i) 
    ...:                                                                                                                                                                     
a
b
c
```



### for... else 

完成循环，就执行else

```
guess_num = 34

for i in range(1):
    my_num = int(input("my_num: "))
    if my_num == guess_num:
        print('Yes')
        break
    else:
        print("No")
else:
    print('This is else from for circulation.')
>>>
my_num: 1
No
This is else from for circulation.
```



破坏循环，跳过else

```
guess_num = 34

for i in range(1):
    my_num = int(input("my_num: "))
    if my_num == guess_num:
        print('Yes')
        break
    else:
        print("No")
else:
    print('This is else from for circulation.')
>>>
my_num: 34
Yes
```





## for vs while

下面二者等价

```python
i = 0
while i < 1000000:
    i += 1

for i in range(0, 1000000):
    pass
```

要知道，range()函数是直接由C语言写的，调用它速度非常快。而while循环中的“i += 1”这个操作，得通
过Python的解释器间接调用底层的C语言;并且这个简单的操作，又涉及到了对象的创建和删除(因为i是整
型，是immutable，i += 1相当于i = new int(i + 1))。所以，显然，for循环的效率更胜一筹。





## break

结束整个循环, 以后的循环都不会被执行

```
for i in range(5):
    if i == 2:
        break
    print(i)
>>>
0
1
```



循环嵌套中，只会影响语句所在的那一层循环

```
In [17]: for i in range(0, 3):
    ...:     print(i)
    ...:     for j in range(10, 16):
    ...:         if j == 11:
    ...:             break
    ...:         print(j)
    ...:
0
10
1
10
2
10
```



## continue

跳出本次循环，进入下一次循环



```
for i in range(5):
    if i == 2:
        continue
    print(i)
>>>
0
1
3
4
```



循环嵌套中，只会影响语句所在的那一层循环

```
In [18]: for i in range(0, 3):
    ...:     print(i)
    ...:     for j in range(10, 16):
    ...:         if j == 11:
    ...:             continue
    ...:         print(j)
    ...:
0
10
12
13
14
15
1
10
12
13
14
15
2
10
12
13
14
15
```





# 表驱动法

表格驱动的意义在于：逻辑和数据分离。它的本质是，从表里查询信息来代替逻辑语句（if,case）。

在程序中，添加数据和逻辑的方式是不一样的，成本也是不一样的。简单的说，数据的添加是非常简单，低成本和低风险的；而逻辑的添加是复杂，高成本和高风险的。



## 优势

逻辑和数据区分一目了然
关系表可以更换，比如国家表格可以是多语言的，中文版表格，英文版表格等，日语版表格，以及单元测试中，可以注入测试表格。
在单元测试中，逻辑必须测试，而数据无需测试。
可以想象如果没有表格法，弄个多语言，要写多少语句。



## 写法

每个月对应多少天，传统写法

```
int getMonthDays(int month){
    switch(month){
        case 1:return 31;break;
        case 2:return 29;break;
        case 3:return 31;break;
        case 4:return 30;break;
        case 5:return 31;break;
        case 6:return 30;break;
        case 7:return 31;break;
        case 8:return 31;break;
        case 9:return 30;break;
        case 10:return 31;break;
        case 11:return 30;break;
        case 12:return 31;break;
        default：return 0;
    }
}
```



表驱动写法

```
int monthDays[12] = {31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
int getMonthDays(int month){
    return monthDays[--month];
}
```





# 循环优化



## if

if语句几乎总是有两个分支。它们有可能嵌套，有多层的缩进，而且else分支里面有可能出现少量重复的代码。

然而这样的结构，逻辑却非常严密和清晰。

```
if (...) {
  if (...) {
    ...
    return false;
  } else {
    return true;
  }
} else if (...) {
  ...
  return false;
} else {
  return true;
}
```

使用这种方式，其实是为了无懈可击的处理所有可能出现的情况，避免漏掉corner case。每个if语句都有两个分支的理由是：如果if的条件成立，你做某件事情；但是如果if的条件不成立，你应该知道要做什么另外的事情。不管你的if有没有else，你终究是逃不掉，必须得思考这个问题的。



另一种省略else分支的情况是这样：

```
String s = "";
if (x < 5) {
  s = "ok";
}
```

写这段代码的人，脑子里喜欢使用一种“缺省值”的做法。`s`缺省为null，如果x<5，那么把它改变（mutate）成“ok”。这种写法的缺点是，当`x<5`不成立的时候，你需要往上面看，才能知道s的值是什么。这还是你运气好的时候，因为s就在上面不远。很多人写这种代码的时候，s的初始值离判断语句有一定的距离，中间还有可能插入一些其它的逻辑和赋值操作。这样的代码，把变量改来改去的，看得人眼花，就容易出错。

现在比较一下我的写法：

```
String s;
if (x < 5) {
  s = "ok";
} else {
  s = "";
}
```

这种写法貌似多打了一两个字，然而它却更加清晰。这是因为我们明确的指出了`x<5`不成立的时候，s的值是什么。它就摆在那里，它是`""`（空字符串）。注意，虽然我也使用了赋值操作，然而我并没有“改变”s的值。s一开始的时候没有值，被赋值之后就再也没有变过。我的这种写法，通常被叫做更加“函数式”，因为我只赋值一次。

如果我漏写了else分支，Java编译器是不会放过我的。它会抱怨：“在某个分支，s没有被初始化。”这就强迫我清清楚楚的设定各种条件下s的值，不漏掉任何一种情况。

当然，由于这个情况比较简单，你还可以把它写成这样：

```
String s = x < 5 ? "ok" : "";
```

对于更加复杂的情况，我建议还是写成if语句为好。







## 避免使用continue和break

循环语句（for，while）里面出现return是没问题的，然而如果你使用了continue或者break，就会让循环的逻辑和终止条件变得复杂，难以确保正确。

出现continue或者break的原因，往往是对循环的逻辑没有想清楚。如果你考虑周全了，应该是几乎不需要continue或者break的。如果你的循环里出现了continue或者break，你就应该考虑改写这个循环。改写循环的办法有多种：

1. 如果出现了continue，你往往只需要把continue的条件反向，就可以消除continue。
2. 如果出现了break，你往往可以把break的条件，合并到循环头部的终止条件里，从而去掉break。
3. 有时候你可以把break替换成return，从而去掉break。
4. 如果以上都失败了，你也许可以把循环里面复杂的部分提取出来，做成函数调用，之后continue或者break就可以去掉了。



## 提前判断返回

提前判断返回：其实也可以理解为卫语句，卫语句就是把复杂的条件表达式拆分成多个条件表达式，比如一个很复杂的表达式，嵌套了好几层的 if-then-else 语句，转换为多个 if 语句，实现它的逻辑，这多条的 if 语句就是卫语句。最终要达到的目的就是提前判断返回。

```
if(condition){
   //dost
}else{
   return ;
}
```

改为

```
if(!condition){
   return ;
}
//dost
```