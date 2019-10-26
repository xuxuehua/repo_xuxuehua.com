---
title: "circulation"
date: 2019-10-07 02:15
---
[TOC]



# for循环

for`循环的功能非常强大，它使用计数器实现循环。`for`循环会先初始化计数器，然后，在每次循环前检测循环条件，在每次循环后更新计数器。计数器变量通常命名为`i

```
for (初始条件; 循环检测条件; 循环后更新计数器) {
    // 执行语句
}
```



整型数组的所有元素求和

```
public class Main {
    public static void main(String[] args) {
        int [] ns = {1, 4, 9, 16, 25};
        int sum = 0;
        for (int i=0; i<ns.length; i++) {
            System.out.println("i = " + i + ", ns[i] = " + ns[i]);
            sum = sum + ns[1];
        }
        System.out.println("sum = " + sum);
    }
}

>>>
i = 0, ns[i] = 1
i = 1, ns[i] = 4
i = 2, ns[i] = 9
i = 3, ns[i] = 16
i = 4, ns[i] = 25
sum = 20
```



## 灵活使用for循环 (不推荐)

`for`循环还可以缺少初始化语句、循环条件和每次循环更新语句，例如：

```
// 不设置结束条件:
for (int i=0; ; i++) {
    ...
}
// 不设置结束条件和更新语句:
for (int i=0; ;) {
    ...
}
// 什么都不设置:
for (;;) {
    ...
}
```

通常不推荐这样写，但是，某些情况下，是可以省略`for`循环的某些语句的





# for each循环

`for`循环经常用来遍历数组，因为通过计数器可以根据索引来访问数组的每个元素



## 遍历数组

`for each`循环的写法也更简洁。但是，`for each`循环无法指定遍历顺序，也无法获取数组的索引

```
public class Main {
    public static void main(String[] args) {
        int [] ns = {1, 4, 9, 16, 25};
        for (int n : ns) {
            System.out.println(n);
        }
    }
}

>>>
1
4
9
16
25
```



```
public class Main {
    public static void main(String[] args) {
        int [] ns = {1, 4, 9, 16, 25};
        for (int i=ns.length-1; i>=0; i--) {
            System.out.println(ns[i]);
        }
    }
}

>>>
25
16
9
4
1
```



# while循环

循环语句就是让计算机根据条件做循环计算，在条件满足时继续循环，条件不满足时退出循环。

```
while (条件表达式) {
    循环语句
}
// 继续执行后续代码
```



```
public class Main {
    public static void main(String[] args) {
        int sum = 0;
        int m = 20;
        int n = 100;
        while (m<=n) {
            sum += m;
            m++;
        }
        System.out.println(sum);
    }
}

>>>
4860
```





# do while循环

`do while`循环则是先执行循环，再判断条件，条件满足时继续循环，条件不满足时退出

`do while`循环会至少循环一次

```
do {
    执行循环语句
} while (条件表达式);
```



```
public class Main {
    public static void main(String[] args) {
        int sum = 0;
        int n = 1;
        do {
            sum = sum + n;
            n++;
        } while (n <= 100);
            System.out.println(sum);
    }
}

>>>
5050
```





# break

`break`语句通常都是配合`if`语句使用。要特别注意，`break`语句总是跳出自己所在的那一层循环

```
public class Main {
    public static void main(String[] args) {
        for (int i=1; i<=10; i++) {
            System.out.println("i = " + i);
            for (int j=1; j<=10; j++) {
                System.out.println("j = " + j);
                if (j >= i) {
                    break;
                }
            }
            // break跳到这里
            System.out.println("breaked");
        }
    }
}

>>>
i = 1
j = 1
Breaked
i = 2
j = 1
j = 2
Breaked
i = 3
j = 1
j = 2
j = 3
Breaked
i = 4
j = 1
j = 2
j = 3
j = 4
Breaked
i = 5
j = 1
j = 2
j = 3
j = 4
j = 5
Breaked
i = 6
j = 1
j = 2
j = 3
j = 4
j = 5
j = 6
Breaked
i = 7
j = 1
j = 2
j = 3
j = 4
j = 5
j = 6
j = 7
Breaked
i = 8
j = 1
j = 2
j = 3
j = 4
j = 5
j = 6
j = 7
j = 8
Breaked
i = 9
j = 1
j = 2
j = 3
j = 4
j = 5
j = 6
j = 7
j = 8
j = 9
Breaked
i = 10
j = 1
j = 2
j = 3
j = 4
j = 5
j = 6
j = 7
j = 8
j = 9
j = 10
Breaked
```



# continue

`continue`则是提前结束本次循环，直接继续执行下次循环

在多层嵌套的循环中，`continue`语句同样是结束本次自己所在的循环

```
public class Main {
    public static void main(String[] args) {
        int sum = 0;
        for (int i=1; i<=10; i++) {
            System.out.println("begin i = " + i);
            if (i % 2 == 0) {
                continue; // continue语句会结束本次循环
            }
            sum = sum + i;
            System.out.println("end i = " + i);
        }
        System.out.println(sum); // 25
    }
}

>>>
begin i = 1
end i = 1
begin i = 2
begin i = 3
end i = 3
begin i = 4
begin i = 5
end i = 5
begin i = 6
begin i = 7
end i = 7
begin i = 8
begin i = 9
end i = 9
begin i = 10
25
```



