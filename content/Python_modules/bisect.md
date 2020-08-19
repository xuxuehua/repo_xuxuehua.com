---
title: "bisect"
date: 2020-08-09 17:00
---
[toc]



# bisect 维护已排序的序列

才用二分查找的方式操作

## insort 插入方法

insort_left

insort_right

```
import bisect

inter_list = []

bisect.insort(inter_list, 3)
bisect.insort(inter_list, 2)
bisect.insort(inter_list, 5)
bisect.insort(inter_list, 1)
bisect.insort(inter_list, 6)
bisect.insort(inter_list, 8)
print(inter_list)

>>>
[1, 2, 3, 5, 6, 8]
```



## bisect 查找方法

bisect_left

bisect_right

```
import bisect

inter_list = []

bisect.insort(inter_list, 3)
bisect.insort(inter_list, 2)
bisect.insort(inter_list, 5)
bisect.insort(inter_list, 1)
bisect.insort_left(inter_list, 6)
bisect.insort(inter_list, 8)
bisect.insort(inter_list, 8)
print(bisect.bisect(inter_list, 3))
print(bisect.bisect(inter_list, 6))
bisect.insort_left(inter_list, 6)
print(bisect.bisect(inter_list, 6))

>>>
3
5
6
```





