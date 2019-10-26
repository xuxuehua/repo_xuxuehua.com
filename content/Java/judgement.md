---
title: "judgement"
date: 2019-10-07 00:45
---
[TOC]



# If 判断

根据`if`的计算结果（`true`还是`false`），JVM决定是否执行`if`语句块（即花括号{}包含的所有语句）

```
if (条件) {
    // 条件满足时执行
}
```



## else

`if`语句还可以编写一个`else { ... }`，当条件判断为`false`时，将执行`else`的语句块





# 引用判断

在Java中，判断值类型的变量是否相等，可以使用`==`运算符。但是，判断引用类型的变量是否相等，`==`表示“引用是否相等”，或者说，是否指向同一个对象。例如，下面的两个String类型，它们的内容是相同的，但是，分别指向不同的对象，用`==`判断，结果为`false`



要判断引用类型的变量内容是否相等，必须使用`equals()`方法

```
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "HELLO".toLowerCase();
        System.out.println(s1);
        System.out.println(s2);
        if (s1.equals(s2)) {
            System.out.println("s1 equals s2");
        } else {
            System.out.println("s1 not equals s2");
        }
    }
}
hello
hello
s1 equals s2
```



执行语句`s1.equals(s2)`时，如果变量`s1`为`null`, 会报`NullPointerException`, 要避免`NullPointerException`错误，可以利用短路运算符`&&`

```
public class Main {
    public static void main(String[] args) {
        String s1 = null;
        if (s1 != null && s1.equals("hello")) {
            System.out.println("hello");
        }
    }
}
```







# switch多重选择

`switch`语句根据`switch (表达式)`计算的结果，跳转到匹配的`case`结果，然后继续执行后续语句，直到遇到`break`结束执行

如果`option`的值没有匹配到任何`case`，例如`option = 99`，那么，`switch`语句不会执行任何语句。这时，可以给`switch`语句加一个`default`，当没有匹配到任何`case`时，执行`default`

任何时候都不要忘记写`break`, 否则后续语句将全部执行

```
public class Main {
    public static void main(String[] args) {
        int option = 99;
        switch (option) {
            case 1:
                System.out.println("Select 1");
                break;
            case 2:
                System.out.println("Select 2");
                break;
            case 3:
                System.out.println("Select 3");
            default:
                System.out.println("Not selected");
                break;
        }
    }
}

>>>
Not selected
```



## switch 语法检查

在Idea中，选择`Preferences` - `Editor` - `Inspections` - `Java` - `Control flow issues`，将以下检查标记为Warning：

- Fallthrough in 'switch' statement
- 'switch' statement without 'default' branch

当`switch`语句存在问题时，即可在IDE中获得警告提示



## switch表达式

从Java 12开始，`switch`语句升级为更简洁的表达式语法，使用类似模式匹配（Pattern Matching）的方法，保证只有一种路径会被执行，并且不需要`break`语句

注意新语法使用`->`，如果有多条语句，需要用`{}`括起来。不要写`break`语句，因为新语法只会执行匹配的语句，没有穿透效应

```
public class Main {
    public static void main(String[] args) {
        String fruit = "orange";
        int opt = switch (fruit) {
            case "apple" -> 1;
            case "pear", "orange" -> 2;
            default -> 0;
        };
        System.out.println("opt = " + opt);
    }
}
>>>
opt = 2
```



## yield

大多数时候，在`switch`表达式内部，我们会返回简单的值。

但是，如果需要复杂的语句，我们也可以写很多语句，放到`{...}`里，然后，用`yield`返回一个值作为`switch`语句的返回值

```
public class Main {
    public static void main(String[] args) {
        String fruit = "blueberry";
        int opt = switch (fruit) {
            case "apple" -> 1;
            case "pear", "orange" -> 2;
            default -> {
                int code = fruit.hashCode();
                yield code;
            }
        };
        System.out.println("opt = " + opt);
    }
}

>>>
opt = 1951963900
```





