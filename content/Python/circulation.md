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

| 对象/常量 | True or False |
| :-------- | ------------- |
| ""        | False         |
| "string"  | True          |
| 0         | False         |
| \>=1      | True          |
| \<=-1     | True          |
| () 空元组 | False         |
| [] 空列表 | False         |
| {} 空字典 | False         |
| None      | False         |



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





## break

结束整个循环

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

