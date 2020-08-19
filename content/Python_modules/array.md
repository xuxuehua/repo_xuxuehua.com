---
title: "array"
date: 2020-08-09 17:01
---
[toc]



# array 数组

与list的重要区别是，array只能存放指定的数据类型



Return a new array whose items are restricted by typecode, and initialized from the optional initializer value, which must be a list, string or iterable over elements of the appropriate type.



## 类型

Arrays represent basic values and behave very much like lists, except the type of objects stored in them is constrained. The type is specified at object creation time by using a type code, which is a single character. The following type codes are defined:

| Type code | C Type            | Minimum size in bytes |
| --------- | ----------------- | --------------------- |
| 'b'       | signed integer    | 1                     |
| 'B'       | unsigned integer  | 1                     |
| 'u'       | Unicode character | 2 (see note)          |
| 'h'       | signed integer    | 2                     |
| 'H'       | unsigned integer  | 2                     |
| 'i'       | signed integer    | 2                     |
| 'I'       | unsigned integer  | 2                     |
| 'l'       | signed integer    | 4                     |
| 'L'       | unsigned integer  | 4                     |
| 'q'       | signed integer    | 8 (see note)          |
| 'Q'       | unsigned integer  | 8 (see note)          |
| 'f'       | floating point    | 4                     |
| 'd'       | floating point    | 8                     |

> The 'u' typecode corresponds to Python's unicode character. On narrow builds this is 2-bytes on wide builds this is 4-bytes.
>
> The 'q' and 'Q' type codes are only available if the platform C compiler used to build Python supports 'long long', or, on Windows, '__int64'.



# 包含方法

## append()

append a new item to the end of the array 



## buffer_info()

return information giving the current memory info 



## byteswap() 

byteswap all the items of the array 



## count()

return number of occurrences of an object 



## extend() 

extend array by appending multiple elements from an iterable 



## fromfile() 

read items from a file object 



## fromlist() 

append items from the list 



## frombytes() 

append items from the string 



## index() 

return index of first occurrence of an object 



## insert() 

insert a new item into the array at a provided position 



## pop() 

remove and return item (default last) 



## remove() 

remove first occurrence of an object 



## reverse() 

reverse the order of the items in the array 



## tofile() 

write all items to a file object 



## tolist() 

return the array converted to an ordinary list 



## tobytes() 

return the array converted to a string



## typecode 属性 

the typecode character used to create the array 

## itemsize 属性

the length in bytes of one array item

# Hello world

```
import array

my_array = array.array('i')
my_array.append(1)
print(my_array)

>>>
array('i', [1])
```

