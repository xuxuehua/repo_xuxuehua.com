---
title: "function 函数"
date: 2018-06-10 19:44
collection: 函数
---

[TOC]



# Function



## 定义函数

```
function functionName() {
    //codes
}
```



函数体内部的语句在执行时，一旦执行到`return`时，函数就执行完毕

如果没有`return`语句，函数执行完毕后也会返回结果，只是结果为`undefined`

```
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```





### 将函数作为函数对象 (ES5)

`function (x) { ... }`是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量`abs`，所以，通过变量`abs`就可以调用该函数

```
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};
```



### 箭头函数 (ES6)

```
let sq = (x) => {return x*x;};
or
let sq = (x) => x*x;
```



```
sq = (x) => x*x;
(x) => x*x
sq(5)
25
```




## 调用函数

调用函数时，按顺序传入参数即可

```
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
>>>
abs(10); // 返回10
abs(-9); // 返回9
```

允许传入任意个参数而不影响调用，因此传入的参数比定义的参数多也没有问题

```
abs(10, 'blablabla'); // 返回10
abs(-9, 'haha', 'hehe', null); // 返回9
```

* 传入的参数比定义的少也没有问题：

```
abs(); // 返回NaN
```





### 避免`undefined`

```
function abs(x) {
    if (typeof x !== 'number') {
        throw 'Not a number';
    }
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```



## 函数参数



### 关键字参数`arguments`



```
function myFunction(arg1, arg2,...) {
    //codes
}
```



关键字`arguments`，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。`arguments`类似`Array`但它不是一个`Array`

```
'use strict';
function foo(x) {
    console.log('x = ' + x); // 10
    for (var i=0; i<arguments.length; i++) {
        console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
    }
}
foo(10, 20, 30);
>>>
x = 10
arg 0 = 10
arg 1 = 20
arg 2 = 30
```



#### 传入空参数

```
利用arguments，你可以获得调用者传入的所有参数。也就是说，即使函数不定义任何参数，还是可以拿到参数的值：

function abs() {
    if (arguments.length === 0) {
        return 0;
    }
    var x = arguments[0];
    return x >= 0 ? x : -x;
}
>>>
abs(); // 0
abs(10); // 10
abs(-9); // 9
```



#### 判断传入参数

* 实际上arguments最常用于判断传入参数的个数。你可能会看到这样的写法：

```
// foo(a[, b], c)
// 接收2~3个参数，b是可选参数，如果只传2个参数，b默认为null：
function foo(a, b, c) {
    if (arguments.length === 2) {
        // 实际拿到的参数是a和b，c为undefined
        c = b; // 把b赋给c
        b = null; // b变为默认值
    }
    // ...
}
```

> 要把中间的参数`b`变为“可选”参数，就只能通过`arguments`判断，然后重新调整参数并赋值



### rest参数

* 由于JavaScript函数允许接收任意个参数，于是我们就不得不用`arguments`来获取所有参数：

```
function foo(a, b) {
    var i, rest = [];
    if (arguments.length > 2) {
        for (i = 2; i<arguments.length; i++) {
            rest.push(arguments[i]);
        }
    }
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}
```

* 为了获取除了已定义参数`a`、`b`之外的参数，我们不得不用`arguments`，并且循环要从索引`2`开始以便排除前两个参数，这种写法很别扭，只是为了获得额外的`rest`参数，有没有更好的方法？

* rest参数只能写在最后，前面用`...`标识，从运行结果可知，传入的参数先绑定`a`、`b`，多余的参数以数组形式交给变量`rest`，所以，不再需要`arguments`我们就获取了全部参数

```
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```



#### 计算求和

```
'use strict';
function sum(...rest) {
    var sum = 0;
    for (let i=0; i<rest.length; i++) {
        sum += rest[i];
    }
    return sum;
};

var i, args = [];
for (i=1; i<=100; i++) {
    args.push(i);
}
if (sum() !== 0) {
    console.log('测试失败: sum() = ' + sum());
} else if (sum(1) !== 1) {
    console.log('测试失败: sum(1) = ' + sum(1));
} else if (sum(2, 3) !== 5) {
    console.log('测试失败: sum(2, 3) = ' + sum(2, 3));
} else if (sum.apply(null, args) !== 5050) {
    console.log('测试失败: sum(1, 2, 3, ..., 100) = ' + sum.apply(null, args));
} else {
    console.log('测试通过!');
}
```



## 函数中的变量

### 局部 JavaScript 变量

在 JavaScript 函数内部声明的变量（使用 var）是*局部*变量，所以只能在函数内部访问它。（该变量的作用域是局部的）。

您可以在不同的函数中使用名称相同的局部变量，因为只有声明过该变量的函数才能识别出该变量。

只要函数运行完毕，本地变量就会被删除。



### 全局 JavaScript 变量

在函数外声明的变量是*全局*变量，网页上的所有脚本和函数都能访问它。



## 函数变量作用域

* 如果一个变量在函数体内部申明，则该变量的作用域为整个函数体，在函数体外不可引用该变量：

```
'use strict';

function foo() {
    var x = 1;
    x = x + 1;
}

x = x + 2; // ReferenceError! 无法在函数体外引用变量x
```



* 如果两个不同的函数各自申明了同一个变量，那么该变量只在各自的函数体内起作用。换句话说，不同函数内部的同名变量互相独立，互不影响：

```
'use strict';

function foo() {
    var x = 1;
    x = x + 1;
}

function bar() {
    var x = 'A';
    x = x + 'B';
}
```



* 由于JavaScript的函数可以嵌套，此时，内部函数可以访问外部函数定义的变量，反过来则不行：

```
'use strict';

function foo() {
    var x = 1;
    function bar() {
        var y = x + 1; // bar可以访问foo的变量x!
    }
    var z = y + 1; // ReferenceError! foo不可以访问bar的变量y!
}
```





### 变量提升

* JavaScript的函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量“提升”到函数顶部：

```
'use strict';

function foo() {
    var x = 'Hello, ' + y;
    console.log(x);
    var y = 'Bob';
}

foo();
```

> 虽然是strict模式，但语句`var x = 'Hello, ' + y;`并不报错，原因是变量`y`在稍后申明了。但是`console.log`显示`Hello, undefined`，说明变量`y`的值为`undefined`。这正是因为JavaScript引擎自动提升了变量`y`的声明，但不会提升变量`y`的赋值
>
> * 对于上述`foo()`函数，JavaScript引擎看到的代码相当于：
>
> ```
> function foo() {
>     var y; // 提升变量y的申明，此时y为undefined
>     var x = 'Hello, ' + y;
>     console.log(x);
>     y = 'Bob';
> }
> ```



### 全局作用域

不在任何函数内定义的变量就具有全局作用域。实际上，JavaScript默认有一个全局对象`window`，全局作用域的变量实际上被绑定到`window`的一个属性

```
'use strict';

var course = 'Learn JavaScript';
alert(course); // 'Learn JavaScript'
alert(window.course); // 'Learn JavaScript'
```

* 以变量方式`var foo = function () {}`定义的函数实际上也是一个全局变量，因此，顶层函数的定义也被视为一个全局变量，并绑定到`window`对象

```
'use strict';

function foo() {
    alert('foo');
}

foo(); // 直接调用foo()
window.foo(); // 通过window.foo()调用
```

 

####  减少全局变量冲突

* 减少冲突的一个方法是把自己的所有变量和函数全部绑定到一个全局变量中

```
// 唯一的全局变量MYAPP:
var MYAPP = {};

// 其他变量:
MYAPP.name = 'myapp';
MYAPP.version = 1.0;

// 其他函数:
MYAPP.foo = function () {
    return 'foo';
};
```




### 局部作用域

* 由于JavaScript的变量作用域实际上是函数内部，我们在`for`循环等语句块中是无法定义具有局部作用域的变量的

```
'use strict';

function foo() {
    for (var i=0; i<100; i++) {
        //
    }
    i += 100; // 仍然可以引用变量i
}
```



#### 解决块级作用域 -> Let

为了解决块级作用域，ES6引入了新的关键字`let`，用`let`替代`var`可以申明一个块级作用域的变量， 即在函数内部有效

```
'use strict';

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```



## 常量

* `var`和`let`申明的是变量
* ES6标准引入了新的关键字`const`来定义常量，`const`与`let`都具有块级作用域
* 通常用全部大写的变量来表示“这是一个常量，不要修改它的值”

```
'use strict';

const PI = 3.14;
PI = 3; // 某些浏览器不报错，但是无效果！
PI; // 3.14
```



## 解构赋值

* 从ES6开始，JavaScript引入了解构赋值，可以同时对一组变量进行赋值
* 传统的做法，如何把一个数组的元素分别赋值给几个变量

```
var array = ['hello', 'JavaScript', 'ES6'];
var x = array[0];
var y = array[1];
var z = array[2];
```



* 解构赋值，直接对多个变量同时赋值

```
'use strict'
var [x, y, z] = ['hello', 'Javascript', 'ES6']
console.log('x = ' + x + ', y = ' + y + ', z = ' + z)
>>>
x = hello, y = Javascript, z = ES6
```



### 嵌套中的解析赋值

* 如果数组本身还有嵌套，也可以通过下面的形式进行解构赋值，注意嵌套层次和位置要保持一致

```
let [x, [y, z]] = ['hello', ['Javascript', 'ES6']];
x;	// 'hello'
y;	// 'Javascript'
z;	// 'ES6'
```



* 解构赋值还可以忽略某些元素

```
let [, , z] = ['hello', 'Javascript', 'ES6'];
z; 	// 'ES6'
```



* 从一个对象中取出若干属性，也可以使用解构赋值，便于快速获取对象的指定属性

```
'use strict';
 var person = {
     name: 'Rick',
     age: 18,
     gender: 'male'
 };
 var {name, age, gender} = person
 console.log('name = ' + name + ', age = ' + age + ', gender = ' + gender);
 >>> 
 name = Rick, age = 18, gender = male
```



### 对象解析赋值

* 对一个对象进行解构赋值时，同样可以直接对嵌套的对象属性进行赋值，只要保证对应的层次是一致的

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```



* 使用解构赋值对对象属性进行赋值时，如果对应的属性不存在，变量将被赋值为`undefined`，这和引用一个不存在的属性获得`undefined`是一致的。如果要使用的变量名和属性名不一致，可以用下面的语法获取

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};

// 把passport属性赋值给变量id:
let {name, passport:id} = person;
name; // '小明'
id; // 'G-12345678'
// 注意: passport不是变量，而是为了让变量id获得passport属性:
passport; // Uncaught ReferenceError: passport is not defined
```



### 解析赋值使用默认值

* 解构赋值还可以使用默认值，这样就避免了不存在的属性返回`undefined`的问题

```
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678'
};

// 如果person对象没有single属性，默认赋值为true:
var {name, single=true} = person;
name; // '小明'
single; // true
```



* 有些时候，如果变量已经被声明了，再次赋值的时候，正确的写法也会报语法错误

```
// 声明变量:
var x, y;
// 解构赋值:
{x, y} = { name: '小明', x: 100, y: 200};
// 语法错误: Uncaught SyntaxError: Unexpected token =
```

* 这是因为JavaScript引擎把`{`开头的语句当作了块处理，于是`=`不再合法。解决方法是用小括号括起来：

```
({x, y} = { name: '小明', x: 100, y: 200});
```



### 解析赋值使用场景

#### 交换变量的值

```
var x=1, y=2;
[x, y] = [y, x]
```

* 快速获取当前页面的域名和路径：

```
var {hostname:domain, pathname:path} = location;
```



* 下面的函数可以快速创建一个`Date`对象

```
function buildDate({year, month, day, hour=0, minute=0, second=0}) {
    return new Date(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second);
}

buildDate({ year: 2017, month: 1, day: 1 });
>>>
Sun Jan 01 2017 00:00:00 GMT+0800 (CST)

buildDate({ year: 2017, month: 1, day: 1, hour: 20, minute: 15 });
>>>
 Sun Jan 01 2017 20:15:00 GMT+0800 (CST)
```



## 方法

* 在一个对象中绑定函数，称为这个对象的方法, 和普通函数也没啥区别，但是它在内部使用了一个`this`关键字

```
var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var y = new Date().getFullYear();
        return y - this.birth;
    }
};

xiaoming.age; // function xiaoming.age()
xiaoming.age(); // 今年调用是25,明年调用就变成26了
```



### this特殊变量

在一个方法内部，`this`是一个特殊变量，它始终指向当前对象，也就是`xiaoming`这个变量。所以，`this.birth`可以拿到`xiaoming`的`birth`属性。

让我们拆开写：

```
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25, 正常结果
getAge(); // NaN
```

> JavaScript的函数内部如果调用了`this`，那么这个`this`到底指向谁？
>
> 答案是，视情况而定！
>
> 如果以对象的方法形式调用，比如`xiaoming.age()`，该函数的`this`指向被调用的对象，也就是`xiaoming`，这是符合我们预期的。
>
> 如果单独调用函数，比如`getAge()`，此时，该函数的`this`指向全局对象，也就是`window`



* 修复的办法也不是没有，我们用一个`that`变量首先捕获`this`

```
'use strict';

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        var that = this; // 在方法内部一开始就捕获this
        function getAgeFromBirth() {
            var y = new Date().getFullYear();
            return y - that.birth; // 用that而不是this
        }
        return getAgeFromBirth();
    }
};

xiaoming.age(); // 25
```

> 用`var that = this;`，你就可以放心地在方法内部定义其他函数，而不是把所有语句都堆到一个方法中



### apply 方法

* 虽然在一个独立的函数调用中，根据是否是strict模式，`this`指向`undefined`或`window`，不过，我们还是可以控制`this`的指向的！

  要指定函数的`this`指向哪个对象，可以用函数本身的`apply`方法，它接收两个参数，第一个参数就是需要绑定的`this`变量，第二个参数是`Array`，表示函数本身的参数。

* 用`apply`修复`getAge()`调用：

```
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
```



### call 方法

* 另一个与`apply()`类似的方法是`call()`，唯一区别是：

- `apply()`把参数打包成`Array`再传入；
- `call()`把参数按顺序传入。
- 比如调用`Math.max(3, 5, 4)`，分别用`apply()`和`call()`实现如下：

```
Math.max.apply(null, [3, 5, 4]); // 5
Math.max.call(null, 3, 5, 4); // 5
```

> 对普通函数调用，我们通常把`this`绑定为`null`









## 装饰器

* 利用`apply()`，我们还可以动态改变函数的行为。
* JavaScript的所有对象都是动态的，即使内置的函数，我们也可以重新指向新的函数。
* 现在假定我们想统计一下代码一共调用了多少次`parseInt()`，可以把所有的调用都找出来，然后手动加上`count += 1`，不过这样做太傻了。最佳方案是用我们自己的函数替换掉默认的`parseInt()`

```
'use strict';
var count = 0;
var oldParseInt = parseInt; // 保存原函数

window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};
// 测试:
parseInt('10');
parseInt('20');
parseInt('30');
console.log('count = ' + count); // 3
```





# 迭代 Recursive

```
function power(base, exponent) {
		if (exponent == 0) {
				return 1;
		} else {
				return base * power(base, exponent - 1)
		}
}

> power(2, 3)
8
```

```
const factorial = n => 
		n === 0
			? 1
			:	n * factorial(n - 1);

> factorial(4)
24
```

