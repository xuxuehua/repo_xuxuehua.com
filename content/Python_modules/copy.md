---
title: "copy"
date: 2018-08-15 10:46
---

[TOC]

# copy 模块



## 浅拷贝(shallow copy) 

指重新分配一块内存，创建一个新的对象，里面的元素是原对象中子对象的引用。因此，如果原对象中的元素不可变，那倒无所谓;但如果元素可变，浅拷贝通常会带来一些副作用，尤其需要注意

```
In [11]: l1 = [[1, 2], (30, 40)]                                                                      

In [12]: l2 = list(l1)                                                                                

In [13]: l1.append(100)                                                                               

In [14]: l1[0].append(3)                                                                              

In [15]: l1                                                                                           
Out[15]: [[1, 2, 3], (30, 40), 100]

In [16]: l2                                                                                           
Out[16]: [[1, 2, 3], (30, 40)]

In [17]: l1[1] += (50, 60)                                                                            

In [18]: l1                                                                                           
Out[18]: [[1, 2, 3], (30, 40, 50, 60), 100]

In [19]: l2                                                                                           
Out[19]: [[1, 2, 3], (30, 40)]
```

> l1.append(100)，表示对l1的列表新增元素100。这个操作不会对l2产生任何影响，因为l2和l1作为整体是两个不同的对象，并不共享内存地址。操作过后l2不变，l1会发生改变
>
> l1[0].append(3)，这里表示对l1中的第一个列表新增元素3。因为l2是l1的浅拷贝，l2中的第一个元素和l1中的第一个元素，共同指向同一个列表，因此l2中的第一个列表也会相对应的新增元素3。操作后l1和l2都会改变
>
> l1[1] += (50, 60)，因为元组是不可变的，这里表示对l1中的第二个元组拼接，然后重新创建了一个新元组作为l1中的第二个元素，而l2中没有引用新元组，因此l2并不受影响。操作后l2不变，l1发生改变







## 深拷贝(deep copy) 

指重新分配一块内存，创建一个新的对象，并且将原对象中的元素，以递归的方式，通过创建新的子对象拷贝到新对象中。因此，新对象和原对象没有任何关联。



```
>>> import copy
>>> origin = [1, 2, [3, 4]]
#origin 里边有三个元素：1， 2，[3, 4]
>>> cop1 = copy.copy(origin)
>>> cop2 = copy.deepcopy(origin)
>>> cop1 == cop2
True
>>> cop1 is cop2
False 
#cop1 和 cop2 看上去相同，但已不再是同一个object
>>> origin[2][0] = "hey!" 
>>> origin
[1, 2, ['hey!', 4]]
>>> cop1
[1, 2, ['hey!', 4]]
>>> cop2
[1, 2, [3, 4]]
#把origin内的子list [3, 4] 改掉了一个元素，观察 cop1 和 cop2
```





不过，深度拷贝也不是完美的，往往也会带来一系列问题。如果被拷贝对象中存在指向自身的引用，那么程序很容易陷入无限循环

```
In [20]: import copy                                                                                  

In [21]: x = [1]                                                                                      

In [22]: x.append(x)                                                                                  

In [23]: x                                                                                            
Out[23]: [1, [...]]

In [24]: y = copy.deepcopy(x)                                                                                                                                                                     

In [25]: y                                                                                            
Out[25]: [1, [...]]
```

> 深度拷贝函数deepcopy中会维护一个字典，记录已经拷贝的对象与其ID。拷贝过程中，如果字典里已经存储了将要拷贝的对象，则会从字典直接返回， 因此并没有出现stack overflow 的情况



