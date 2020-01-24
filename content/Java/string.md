---
title: "string 字符串"
date: 2020-01-21 10:38
---
[toc]



# String 字符串



和`char`类型不同，字符串类型`String`是引用类型，我们用双引号`"..."`表示字符串。一个字符串可以存储0个到任意个字符

```
String s = ""; // 空字符串，包含0个字符
String s1 = "A"; // 包含一个字符
String s2 = "ABC"; // 包含3个字符
String s3 = "中文 ABC"; // 包含6个字符，其中有一个空格
```



## 定义

```
String s = new String("a string");
```

> 创建一个String的对象
>
> 用"a string" 初始化这个对象
>
> 创建管理这个对象的变量s
>
> 让s管理这个对象





## 不可变特性

Java的字符串除了是一个引用类型外，还有个重要特点，就是字符串不可变。

```
public class Main {
    public static void main(String[] args) {
        String s = "Hello";
        System.out.println(s);
        s = "World";
        System.out.println(s);
    }
}

>>>
Hello
World
```

> 原来的字符串`"hello"`还在，只是我们无法通过变量`s`访问它而已。因此，字符串的不可变是指字符串内容不可变。





## 转义

如果字符串本身恰好包含一个`"`字符，编译器就无法判断中间的引号究竟是字符串的一部分还是表示字符串结束。这个时候，我们需要借助转义字符`\`

```
String s = "abc\"xyz"; // 包含7个字符: a, b, c, ", x, y, z
```



### 常见的转义字符

- `\"` 表示字符`"`
- `\'` 表示字符`'`
- `\\` 表示字符`\`
- `\n` 表示换行符
- `\r` 表示回车符
- `\t` 表示Tab
- `\u####` 表示一个Unicode编码的字符





## null  

引用类型的变量可以指向一个空值`null`，它表示不存在，即该变量不指向任何对象

```
String s1 = null; // s1是null
String s2; // 没有赋初值值，s2也是null
String s3 = s1; // s3也是null
String s4 = ""; // s4指向空字符串，不是null
```



# 字符串操作

## `+` 连接

Java的编译器对字符串做了特殊照顾，可以使用`+`连接任意字符串和其他数据类型

```
public class Main {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "world";
        String s = s1 + " " + s2 + "!";
        System.out.println(s);
    }
}

>>>
Hello world!
```



如果用`+`连接字符串和其他数据类型，会将其他数据类型先自动转型为字符串



### 多行字符串连接

如果我们要表示多行字符串，使用+号连接会非常不方便：

```
String s = "first line \n"
         + "second line \n"
         + "end";
```

从Java 13开始，字符串可以用`"""..."""`表示多行字符串（Text Blocks）了

由于多行字符串是作为Java 13的预览特性（Preview Language Features）实现的，编译的时候，我们还需要给编译器加上参数：

```
javac --source 13 --enable-preview Main.java
```



```
public class Main {
    public static void main(String[] args) {
        String s = """
                    select * from
                    users
                    where id > 100
                    order by name desc
                    """;
        System.out.println(s);
    }
}

>>>
select * from
users
where id > 100
order by name desc
```



## `.equals` 内容比较

是否是同一指向

```
if (input == "hello") {
		...
}
```



内容是否相同, string 应该使用.equals 来比较

```
if (input.equals("hello")) {
		...
}
```



## `compareTo` 大小比较

```
public class Main {
    public static void main(String[] args) {
        String s1 = "abc";
        String s2 = "abcd";
        System.out.println(s1.compareTo(s2));
    }
}

>>>
-1
```



## `length` 获取长度

```
public class Main {
    public static void main(String[] args) {
        String s1 = "abc";
        String s2 = "";
        System.out.println(s1.length());
        System.out.println(s2.length());
    }
}

>>>
3
0
```



## `charAt` 访问String里的字符

```
public class Main {
    public static void main(String[] args) {
        String s1 = "abc";
        System.out.println(s1.charAt(2));
    }
}

>>>
c
```



## Substring 得到子串

```
s.substring(n)	// 得到从n号为位置到末尾的全部内容
```



```
s.substring(b,e)	// 得到从b号位置到e号位置之前的内容
```





```
public class Main {
    public static void main(String[] args) {
        String s1 = "0123456789";
        System.out.println(s1.substring(2));
        System.out.println(s1.substring(2,4));
    }
}

>>>
23456789
23
```



## `indexOf` 查找

```
s.indexOf(c)	//得到c字符所在的位置，-1表示不存在
```



```
s.indexOf(c,n)	//得到从n位置开始寻找c字符
```



```
public class Main {
    public static void main(String[] args) {
        String s1 = "0123456789";
        System.out.println(s1.indexOf("4"));
        System.out.println(s1.indexOf("X"));
    }
}

>>>
4
-1
```



```
s.lastIndexOf(c)	
s.lastIndexOf(c,n)
```

> 从右边开始找





## startsWith

```
s.startsWith(t)
```



## endsWith

```
s.endsWith(t)
```



## trim 删除两端空格

```
s.trim()
```



## replace

```
s.replace(c1,c2)
```



## toLowerCase

```
s.toLowerCase()
```



## toUpperCase

```
s.toUpperCase()
```

