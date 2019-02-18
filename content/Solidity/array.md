---
title: "array"
date: 2018-09-02 12:21
---

[TOC]

# 数组



## 动态数组

动态长度：

```
uint[] a
```

length

push()：添加成员



## 静态数组

固定长度数组：

```
uint[5] a
```

length





## struct

1. 通过数组来存储多个员工的信息

   ```
   struct Employee {
       address id;
       uint salary;
       uint lastPayday;
   }
   ```

   - `Employee[] employees`
   - `function addEmployee`
   - `function removeEmployee`

### ==要点==

1. 创建新的`struct`对象不需要`new`：`Employee(employee, salary, now)`
2. 在循环中，若要求的操作已完成，记得使用`return`退出，避免`gas`的无谓消耗
3. `var (empl, i)`可用于接收两个返回值
4. 自定义`struct`不能暴露出去，有`function`需要传入或者返回`struct`类型时需要设置为`private`
5. 直接修改返回的变量改变的只是`memory`中的拷贝；通过`index`来修改变量的话，改变的就是`storage`中的值

### 