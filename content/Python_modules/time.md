---
title: "time"
date: 2018-07-25 13:52
---

[TOC]





# time 模块

实际上，计算机只会维护一个挂钟时间(wall clock time), 这个时间是从某个固定时间起点到现在的时间间隔。

时间起点的选择和计算机相关，但一台计算机的花，这一时间是固定的。



## 相关定义



### UTC

UTC（Coordinated Universal Time）即格林威治天文时间，为世界标准时间。中国北京为UTC+8。



### DST

DST（Daylight Saving Time）即夏令时



### timestamp

时间戳（timestamp）的方式：通常来说，时间戳是指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总秒数

```
time.time()
```





## 常用方法

![img](https://cdn.pbrd.co/images/HAORLLi.png)



### CPU时间

计算机还可以测量cpu实际运行的时间。也就是处理时间(processor clock time)

```
import time
# Wall clock time, unit second
print(time.time())

# Processor clock time, unit second
print(time.clock())
>>>
1481450359.512928
0.062346
```



### 时间戳 -> 元组



#### localtime()

以元组方式返回本地当前时间

```
In [3]: time.localtime()
Out[3]: time.struct_time(tm_year=2018, tm_mon=7, tm_mday=25, tm_hour=13, tm_min=57, tm_sec=25, tm_wday=2, tm_yday=206, tm_isdst=0)
```



#### gmtime()

以元组方式返回格林威治时间

```
In [4]: time.gmtime()
Out[4]: time.struct_time(tm_year=2018, tm_mon=7, tm_mday=25, tm_hour=5, tm_min=58, tm_sec=22, tm_wday=2, tm_yday=206, tm_isdst=0)
```



#### localtime()

返回struct_time 格式的当地时间,当地时区根据系统环境决定

```
In [15]: time.localtime()
Out[15]: time.struct_time(tm_year=2018, tm_mon=10, tm_mday=5, tm_hour=2, tm_min=29, tm_sec=2, tm_wday=4, tm_yday=278, tm_isdst=0)
```





### 元组 -> 时间戳

#### mktime()

将元组时间转换为时间戳

```
In [5]: x =  time.localtime()

In [6]: time.mktime(x)
Out[6]: 1532498377.0
```



### 元组 -> 字符串

#### strftime()

将元组时间转换为字符串格式时间

```
x = time.localtime()
time.strftime('%Y-%m-%d %H:%M:%S',x)
'2017-05-08 16:57:38'
```



### 字符串 -> 元组

#### strptime()

将字符串格式时间转换为元组格式时间
```
time.strptime('2017-05-08 17:03:12','%Y-%m-%d %H:%M:%S')
time.struct_time(tm_year=2017, tm_mon=5, tm_mday=8, tm_hour=17, tm_min=3, tm_sec=12, tm_wday=0, tm_yday=128, tm_isdst=-1)
```



### 元组 -> 指定字符串格式

#### asctime()

元组格式时间转换为字符串格式时间
```
time.asctime()
'Tue May  9 15:23:21 2017'
x = time.localtime()
time.asctime(x)
'Tue May  9 15:23:39 2017'
```



### 时间戳 -> 指定字符串格式

#### ctime()

时间戳转换成字符串格式时间
```
time.ctime()
'Tue May  9 16:07:24 2017'
time.ctime(987867475)
'Sat Apr 21 23:37:55 2001'
```



### 字符串时间格式参照

| 字符串 | 功能                                                         |
| ------ | ------------------------------------------------------------ |
| %a     | 本地（locale）简化星期名称                                   |
| %A     | 本地完整星期名称                                             |
| %b     | 本地简化月份名称                                             |
| %B     | 本地完整月份名称                                             |
| %c     | 本地相应的日期和时间表示                                     |
| %d     | 一个月中的第几天（01 - 31）                                  |
| %H     | 一天中的第几个小时（24小时制，00 - 23）                      |
| %I     | 第几个小时（12小时制，01 - 12）                              |
| %j     | 一年中的第几天（001 - 366）                                  |
| %m     | 月份（01 - 12）                                              |
| %M     | 分钟数（00 - 59）                                            |
| %p     | 本地am或者pm的相应符                                         |
| %S     | 秒（01 - 61）                                                |
| %w     | 一个星期中的第几天（0 - 6，0是星期天）                       |
| %W     | 和%U基本相同，不同的是%W以星期一为一个星期的开始。           |
| %x     | 本地相应日期                                                 |
| %X     | 本地相应时间                                                 |
| %y     | 去掉世纪的年份（00 - 99）                                    |
| %Y     | 完整的年份                                                   |
| %Z     | 时区的名字（如果不存在为空字符）                             |
| %%     | %’字符                                                       |
| %U     | 一年中的周数。（00 - 53，周日是一个周的开始。）第一个星期天之前的所有天数都放在第0周 |

 

 

## sleep()

sleep函数用于将当前线程交出，要求它等待系统将其再次唤醒，如果写程序只有一个线程，这实际上就会阻塞进程，什么也不做。

```
import time
def fucn():
      time.sleep(5)
      print "hello, world"
```

 

 

 





