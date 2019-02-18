---
title: "array 数组"
date: 2018-11-19 21:11
---


[TOC]


# array



## 声明数组

declare -a AA



## 赋值方法

方法1

```
AA[0]=jerry
AA[1]=tom
AA[2]=wendy
```



方法2

```
AA=(jerry tom wendy)
```



方法3

```
AA=([0]=jerry [1]=tom [2]=wendy [6]=natash)
```







## 获取value

```
#!/bin/bash
AA=([0]=jerry [1]=tom [6]=nikita)

for I in {1..20}; do
INDEX=$[$RANDOM%7]
echo ${AA[$INDEX]}
done

echo ${#AA} 第0个字符的个数
echo ${#AA[0]}   同上
echo ${#AA[1]}

echo ${#AA[*]}  引用整个数组当中元素的个数，即数组长度
echo ${#AA[@]} 同上
```



## example

随机10个数，找出最大的数

```
#!/bin/bash
for I in {0..9} ; do
     ARRAY[$I]=$RANDOM
     echo -n “${ARRAY[$I]}  "
     sleep 1
done
echo 
declare -i MAX=${ARRAY[0]}
INDEX=$[${#ARRAY[*]}-1]
for I in `seq 1 $INDEX` ; do
     if [ $MAX -lt ${ARRAY[$I]} ] ; then
          MAX=${ARRAY[$I]}
     fi
done
echo $MAX
```

