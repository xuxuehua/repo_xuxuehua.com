---
title: "arrow"
date: 2019-07-09 11:44
---
[TOC]



# arrow

arrow是一个提供了更易懂和友好的方法来创建、操作、格式化和转化日期、时间和时间戳的python库。可以完全替代datetime，支持python2和3



## 基本用法

```
In [1]: import arrow                                                                                  

In [2]: utc = arrow.utcnow()                                                                          

In [3]: utc                                                                                           
Out[3]: <Arrow [2019-07-09T03:46:05.103876+00:00]>

In [4]: print(utc)                                                                                    
2019-07-09T03:46:05.103876+00:00

In [5]: utc = utc.replace(hours=-1)                                                                   

In [6]: utc                                                                                           
Out[6]: <Arrow [2019-07-09T02:46:05.103876+00:00]>


In [15]: local = utc.to('Asia/ShangHai')                                                              

In [16]: local                                                                                        
Out[16]: <Arrow [2019-07-09T12:27:00.020270+08:00]>

In [17]: local.timestamp                                                                              
Out[17]: 1562646420

In [18]: local.format()                                                                               
Out[18]: '2019-07-09 12:27:00+08:00'

In [19]: local.format('YYYY-MM-DD')                                                                   
Out[19]: '2019-07-09'

In [20]: local.humanize()                                                                             
Out[20]: '2 minutes ago'

In [25]: local.humanize(locale='ko_kr')                                                               
Out[25]: '3분 전'

In [26]: local.humanize(locale='ja_jp')                                                               
Out[26]: '3分前'

In [27]: local.humanize(locale='zh_cn')                                                               
Out[27]: '3分钟前'
```

