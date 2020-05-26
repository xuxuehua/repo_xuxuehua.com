---
title: "array 数组"
date: 2018-06-05 09:42
collection: 基本变量类型
---

[TOC]

# Array 数组	



## 数组定义

数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。

```
[1, 2, 3.14, 'Hello', null, true];
```

> 该数组包含6个元素。数组用`[]`表示，元素之间用`,`分隔

## 创建数组

通过 [ ] 实现

```
var x = [1, 2]
undefined
x
(2) [1, 2]
```



```
let myArray = [{name: "Rick"}, {age: 18}]
```



通过`Array()`函数实现

```
new Array(1, 2, 3); // 创建了数组[1, 2, 3]
```

```
var cars = new Array("Audi", "BMW", "Benz");
```





# 操作数组

## 增

### push

`push()`向`Array`的末尾添加若干元素

```
let myArray = [{name: "Rick"}, {age: 18}];
myArray.push({job: "Engineer"});
myArray

>>>
(3) [{…}, {…}, {…}]
0: {name: "Rick"}
1: {age: 18}
2: {job: "Engineer"}
length: 3
__proto__: Array(0)
```



### unshift 头部添加元素

如果要往`Array`的头部添加若干元素，使用`unshift()`方法

```
var arr = [1, 2];
arr.unshift('A', 'B'); // 返回Array新的长度: 4
arr; // ['A', 'B', 1, 2]
arr.shift(); // 'A'
arr; // ['B', 1, 2]
arr.shift(); arr.shift(); arr.shift(); // 连续shift 3次
arr; // []
arr.shift(); // 空数组继续shift不会报错，而是返回undefined
arr; // []
```





## 删

### pop 尾部删除

`pop()`则把`Array`的最后一个元素删除掉：

```
var arr = [1, 2];
arr.push('A', 'B'); // 返回Array新的长度: 4
arr; // [1, 2, 'A', 'B']
arr.pop(); // pop()返回'B'
arr; // [1, 2, 'A']
arr.pop(); arr.pop(); arr.pop(); // 连续pop 3次
arr; // []
arr.pop(); // 空数组继续pop不会报错，而是返回undefined
arr; // []
```



### shift 头部删除元素

`shift()`方法则把`Array`的第一个元素删掉

```
let myArray = [{name: "Rick"}, {age: 18}]
myArray.shift();
{name: "Rick"}
myArray

>>>
(2) [{…}, {…}]
0: {age: 18}
1: {job: "Engineer"}
length: 2
__proto__: Array(0)
```



## 查



### for i

```
var a = new Array();
undefined
a[0] = '1';
"1"
a[1] = '2';
"2"
a[2] = '3';
"3"
for (i in a) {console.log(a[i])};

>>>
1
2
3
```



### forEach

```
var myArray = [{name: "Rick"}, {age: 18}, {name: "Michelle"}, {age:18}];
const myInfo = info => {console.log(info.name)};
myArray.forEach(myInfo);

>>>
Rick
Michelle
```



### 索引访问数组元素

数组的元素可以通过索引来访问。请注意，索引的起始值为`0`

```
var arr = [1, 2, 3.14, 'Hello', null, true];
arr[0]; // 返回索引为0的元素，即1
arr[5]; // 返回索引为5的元素，即true
arr[6]; // 索引超出了范围，返回undefined
```



### length 获取数组长度

要取得`Array`的长度，直接访问`length`属性：

```
var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6
```



### indexOf

与String类似，`Array`也可以通过`indexOf()`来搜索一个指定的元素的位置：

```
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(20); // 元素20的索引为1
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
```

> 注意了，数字`30`和字符串`'30'`是不同的元素。



## 改



### 数组索引赋值

直接给`Array`的`length`赋一个新的值会导致`Array`大小的变化：

```
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```



Array`可以通过索引把对应的元素修改为新的值，因此，对`Array`的索引进行赋值会直接修改这个`Array：

```
var arr = ['A', 'B', 'C'];
arr[1] = 99;
arr; // arr现在变为['A', 99, 'C']
```
如果通过索引赋值时，索引超过了范围，同样会引起`Array`大小的变化：

```
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```
> 大多数其他编程语言不允许直接改变数组的大小，越界访问索引会报错。然而，JavaScript的`Array`却不会有任何错误。在编写代码时，不建议直接修改`Array`的大小，访问索引时要确保索引不会越界。



### concat 合并数组 

```
var arr1 = new Array(2);
undefined
arr1[0] = 'a';
"a"
arr1[1] = 'b';
"b"
var arr2 = new Array(2);
undefined
arr2[0] = 'c';
"c"
arr2[1] = 'd';
"d"
arr1.concat(arr2)
(4) ["a", "b", "c", "d"]
```









### slice 截取

`slice()`就是对应String的`substring()`版本，它截取`Array`的部分元素，然后返回一个新的`Array`：

```
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']
```

> 注意到`slice()`的起止参数包括开始索引，不包括结束索引。



####复制数组

如果不给`slice()`传递任何参数，它就会从头到尾截取所有元素。利用这一点，我们可以很容易地复制一个`Array`：

```
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
var aCopy = arr.slice();
aCopy; // ['A', 'B', 'C', 'D', 'E', 'F', 'G']
aCopy === arr; // false
```









### sort 

* 字符

`sort()`可以对当前`Array`进行排序，它会直接修改当前`Array`的元素位置，直接调用时，按照默认顺序排序：

```
var arr = ['B', 'C', 'A'];
arr.sort();
arr; // ['A', 'B', 'C']
```



* 数字

```
var arr =  new Array(5);
undefined
arr[0] = '10';
"10"
arr[1] = '5';
"5"
arr[2] = '40'
"40"
arr[3] = '25';
"25"
arr[4] = '1000';
"1000"
function sortNumber(a, b) {return a - b};
arr.sort(sortNumber);

>>>
(5) ["5", "10", "25", "40", "1000"]
```





### reverse

`reverse()`把整个`Array`的元素给掉个方向，也就是反转：

```
var arr = ['one', 'two', 'three'];
arr.reverse(); 
arr; // ['three', 'two', 'one']
```



### splice 分割

`splice()`方法是修改`Array`的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素：

```
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```



### concat 连接

`concat()`方法把当前的`Array`和另一个`Array`连接起来，并返回一个新的`Array`：

```
var arr = ['A', 'B', 'C'];
var added = arr.concat([1, 2, 3]);
added; // ['A', 'B', 'C', 1, 2, 3]
arr; // ['A', 'B', 'C']
```

> `concat()`方法并没有修改当前`Array`，而是返回了一个新的`Array`



`concat()`方法可以接收任意个元素和`Array`，并且自动把`Array`拆开，然后全部添加到新的`Array`里：

```
var arr = ['A', 'B', 'C'];
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]
```



### join 连接

`join()`方法是一个非常实用的方法，它把当前`Array`的每个元素都用指定的字符串连接起来，然后返回连接后的字符串：

```
var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```



## 多维数组

如果数组的某个元素又是一个`Array`，则可以形成多维数组，例如：

```
var arr = [[1, 2, 3], [400, 500, 600], '-'];
```

> 上述`Array`包含3个元素，其中头两个元素本身也是`Array`。

