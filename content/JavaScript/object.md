---
title: "object 对象"
date: 2018-06-05 09:55
collection: 基本变量类型
---

[TOC]

# object 对象



## 创建 JavaScript 对象

* JavaScript的对象是一组由键-值组成的无序集合

```
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};
```

> JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`person`对象一共定义了6个键值对，其中每个键又称为对象的属性，例如，`person`的`name`属性为`'Bob'`，`zipcode`属性为`null`



```
person=new Object();
person.firstname="Bill";
person.lastname="Gates";
person.age=56;
person.eyecolor="blue";
```



### 使用函数来构造对象

```
<!DOCTYPE html>
<html>
<body>

<script>
function person(firstname,lastname,age,eyecolor)
{
this.firstname=firstname;
this.lastname=lastname;
this.age=age;
this.eyecolor=eyecolor;
}

myFather=new person("Bill","Gates",56,"blue");

document.write(myFather.firstname + " is " + myFather.age + " years old.");
</script>

</body>
</html>

>>>
Bill is 56 years old.
```



### 构造器函数内部定义对象的方法

```
<!DOCTYPE html>
<html>
<body>
<script>
function person(firstname,lastname,age,eyecolor)
{
this.firstname=firstname;
this.lastname=lastname;
this.age=age;
this.eyecolor=eyecolor;
 
this.changeName=changeName;
function changeName(name)
{
this.lastname=name;
}
}
myMother=new person("Steve","Jobs",56,"green");
myMother.changeName("Ballmer");
document.write(myMother.lastname);
</script>

</body>
</html>

>>>
Ballmer
```





## 获取对象的属性

* 要获取一个对象的属性，我们用`对象变量.属性名`的方式，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用`''`括起来：
* 或使用`对象名[属性名]` 的方式来访问

```
var xiaohong = {
    name: '小红',
    'middle-school': 'No.1 Middle School'
    };
xiaohong['middle-school']; // 'No.1 Middle School'
xiaohong['name']; // '小红'
xiaohong.name; // '小红'
```

* JavaScript规定，访问不存在的属性不报错，而是返回`undefined`

```
'use strict';

var xiaoming = {
    name: '小明'
};
console.log(xiaoming.name);
console.log(xiaoming.age); // undefined
```



## 添加删除属性

* 由于JavaScript的对象是动态类型，你可以自由地给一个对象添加或删除属性

```
var xiaoming = {
    name: '小明'
};
xiaoming.age; // undefined
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```



### 判断属性是否存在

* 如果我们要检测`xiaoming`是否拥有某一属性，可以用`in`操作符：

```
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```

* 如果`in`判断一个属性存在，这个属性不一定是`xiaoming`的，它可能是`xiaoming`继承得到的

```
'toString' in xiaoming; // true
```

> 因为`toString`定义在`object`对象中，而所有对象最终都会在原型链上指向`object`，所以`xiaoming`也拥有`toString`属性。



* 要判断一个属性是否是`xiaoming`自身拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法：

```
var xiaoming = {
    name: '小明'
};
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```



## 内建方法



### length

```
var test = 'test';
test.length
>>>
4
```



### toUpperCase

```
var message="Hello world!";
var x=message.toUpperCase();
x
>>>
"HELLO WORLD!"
```

