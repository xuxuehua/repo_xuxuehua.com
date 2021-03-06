---
title: "basic_knowledges"
date: 2019-12-11 09:13

---

[toc]

# 数据结构和算法定义

从广义上讲，数据结构就是指一组数据的存储结构。算法就是操作数据的一组方法。 

从狭义上讲，是指某些著名的数据结构和算法，比如队列、栈、堆、二分查找、动态规划等。这些都是前人智慧的结晶，我们可以直接拿来用。我们要讲的这些经典数据结构和算法，都是前人从很多实际操作场景中抽象出来的，经过非常多的求证和检验，可以高效地帮助我们解决很多实际的开发问题。

数据结构和算法是相辅相成的。数据结构是为算法服务的，算法要作用在特定的数据结构之上。 因此，我们无法孤立数据结构来讲算法，也无法孤立算法来讲数据结构。 

## 复杂度分析 （核心要点）

数据结构和算法解决的是如何更省、更快地存储和处理数据的问题，因此，我们就需要一个考量效率和资源消耗的方法，这就是复杂度分析方法

## 常用数据结构

数组、链表、栈、队列、散列表、二叉树、堆、跳表、图、Trie树 

## 常用算法

递归、排序、二分查找、搜索、哈希算法、贪心算法、分治算法、回溯算法、动态规划、字符串匹配算法 

# 大O复杂度表示法

从CPU的角度来看，这段代码的每一行都执行着类似的操作:读数据-运算-写数据。

尽管每行代码对应的CPU执行的个数、执行的时间都不一样，但是，我们这里 只是粗略估计，所以可以假设每行代码执行的时间都一样，为unit_time。 

```java
int cal(int n) {
   int sum = 0;
   int i = 1;
   for (; i <= n; ++i) {
           sum = sum + i;
   } 
return sum;
}
```

> 第2、3行代码分别需要1个unit_time的执行时间
>
> 第4、5行都运行了n遍，所以需要2n*unit_time的执行时间
>
> 所以这段代码总的执行时间就是(2n+2) unit_time



依据上面的思路，下面的代码

```
int cal(int n) {
    int sum = 0;
    int i = 1;
    int j = 1;
    for (; i<=n; ++i) {
        j = 1;
        for (; j<=n; ++j) {
            sum = sum + i * j;
        }
    }
}
```

> 第2、3、4行代码，每行都需要1个unit_time的执行时间
>
> 第5、6行代码循环执行了n遍，需要2n * unit_time的执行时间
>
> 第7、8行代码循环执行了n^2遍，所以需要2n^2 * unit_time的执行时间
>
> 整段代码总的执行时间T(n) = (2n^2+2n+3)*unit_time 



## 公式

$$
T(n) = O(f(n))
$$

> T(n)表示代码执行的时间
> 
> n表示数据规模的大小
> 
> f(n)表示每行代码执行的次数总和, 因为这是一个公式，所以用f(n)来表示。公式中的O，表示代码的执行时间T(n)与f(n)表达式成正比。 



第一个例子中的T(n) = O(2n+2)，第二个例子中的T(n) = O(2n^2+2n+3) 

当n很大时，你可以把它想象成10000、100000。而公式中的低阶、常量、系数三部分并不左右增长趋势，所以都可以忽略。

我们只需要记录一个最大量级就可以 了，如果用大O表示法表示刚讲的那两段代码的时间复杂度，就可以记为:T(n) = O(n); T(n) = O(n^2)。 



## 时间复杂度

大O时间复杂度实际上并不具体表示代码真正的执行时间，而是表示代码执行时间随数据规模增长的变化趋势，所以，也叫作渐进时间复杂度(asymptotic time complexity)，简称时间复杂度 

# 时间复杂度分析

时间复杂度的全称是渐进时间复杂度，表示算法的执行时间与数据规模之间的增长关系

## 单段代码看高频

只关注循环执行次数最多的一段代码 

大O这种复杂度表示方法只是表示一种变化趋势。我们通常会忽略掉公式中的常量、低阶、系数，只需要记录一个最大阶的量级就可以了

在分析一个算法、一段代码的时间复杂度的时候，也只关注循环执行次数最多的那一段代码就可以了

这段核心代码执行次数的n的量级，就是整段要分析代码的时间复杂度

```
int cal(int n) {
    int sum = 0;
    int i = 1;
    for (; i<=n; ++i) {
        sum = sum + i;
    }
    return sum;
}
```

> 其中第2、3行代码都是常量级的执行时间，与n的大小无关，所以对于复杂度并没有影响
>
> 循环执行次数最多的是第4、5行代码，
>
> 所以总的时间复杂度就是O(n) 



## 多段代码取最大

总复杂度等于量级最大的那段代码的复杂度 

```
int cal(int n) {
    int sum_1 = 0;
    int p = 1;
    for (; p<100; ++p) {
        sum_1 = sum_1 + p;
    }

    int sum_2 = 0;
    int q = 1;
    for (; q<n; ++q) {
        sum_2 = sum_2 + q;
    }

    int sum_3 = 0
    int i = 1;
    int j = 1;
    for (; i<=n; ++i) {
        j = 1;
        for (; j<=n; ++j) {
            sum_3 = sum_3 + i * j;
        }
    }
    return sum_1 + sum_2 + sum_3;
}
```

> 这个代码分为三部分，分别是求sum_1、sum_2、sum_3。我们可以分别分析每一部分的时间复杂度，然后把它们放到一块儿，再取一个量级最大的作为整段代码 的复杂度 
> 
> 第一段代码循环执行了100次，是一个常量的执行时间，跟n的规模无关，即便这段代码循环10000次、100000次，只要是一个已知的数，跟n无关，照样也是常量级的执行时间。当n无限大的时候，就可以忽略。尽管对代码的执行时间会有很大影响，但是回到时间复杂度的概念来说，它表示的是一个算法执行效率与数据规模增长的变化趋势，所以不管常量的执行时间多 大，我们都可以忽略掉。因为它本身对增长趋势并没有影响。 
> 
> 第二段代码和第三段代码的时间复杂度是O(n)和O(n^2)  
> 
> 综合这三段代码的时间复杂度，我们取其中最大的量级。所以，整段代码的时间复杂度就为O(n^2)。
> 
> 总的时间复杂度就等于量级最大的那段代码的时间复杂度。那我们将这个规律抽象成公式就是: 
> 
> 如果T1(n)=O(f(n))，T2(n)=O(g(n))
> 
> 那么T(n)=T1(n)+T2(n)=max(O(f(n)), O(g(n))) =O(max(f(n), g(n))) 



## 嵌套代码求乘积/多个规模求加法

嵌套代码的复杂度等于嵌套内外代码复杂度的乘积 

方法有两个参数控制两个循环的次数，那么这时就取二者复杂度相加 

假设T1(n) = O(n)，T2(n) = O(n2)，则T1(n) * T2(n) = O(n3) 

```
int cal(int n) {
    int ret = 0;
    int i = 1;
    for (; i<n; ++i) {
        ret = ret + f(i);
    }
}

int f(int n) {
    int sum = 0;
    int i = 1;
    for (; i<n; ++i) {
        sum = sum + 1;
    }
    return sum;
}
```

> 假设f()只是一个普通的操作，那第4~6行的时间复杂度就是，T1(n) = O(n)
>
> 但f()函数本身不是一个简单的操作，它的时间复杂度是T2(n) = O(n)
>
> 所以，整个cal()函数的时间复杂度就是，T(n) = T1(n) * T2(n) = O(n*n) = O(n^2) 

# 时间复杂度量级(递增)



## 多项式量级

### 常量阶 O(i)

$$
常量阶 O(i)
$$

O(1)只是常量级时间复杂度的一种表示方法，并不是指只执行了一行代码。比如这段代码，即便有3行，它的时间复杂度也是O(1)， 而不是O(3)。 

```
 int i = 8;
 int j = 6;
 int sum = i + j;
```

> 只要代码的执行时间不随n的增大而增长，这样代码的时间复杂度我们都记作O(1)
>
> 或者说，一般情况下，只要算法中不存在循环语句、递归语句，即使有成千上万行的代码，其时间复杂度也是Ο**(1)** 

### 对数阶 O(logn)

#### 对数定义

如果 N=a^x（a>0,a≠1），即*a*的*x*次方等于*N*（*a*>0，且*a*≠1），那么*x*叫做以*a*为底*N*的对数（logarithm），记作：
$$
x=log_aN
$$

其中，*a*叫做对数的底数，*N*叫做真数，*x*叫做 “以*a*为底*N*的对数”


$$
对数阶 O(logn)
$$

对数阶时间复杂度非常常见，同时也是最难分析的一种时间复杂度 

```
i = 1;
while (i <= n) {
    i = i * 2;
} 
```

> 第三行代码是循环执行次数最多的。所以，我们只要能计算出这行代码被执行了多少次，就能知道整段代码的时间复杂度。
> 
> 变量i的值从1开始取，每循环一次就乘以2。当大于n时，循环结束, 实际上，变量i的取值就是一个等比数列 
> $$
> 2^0, 2^1, 2^2 ... 2^k, 2^x = n
> $$
> 只要知道x值是多少，就知道这行代码执行的次数了。通过2^x=n求解x，x=log2n，所以，这段代码的 时间复杂度就是O(log2n) 

把所有对数阶的时间复杂度都记为O(logn)。为什么呢? 

对数之间是可以互相转换的，log3n就等于log32 * log2n，所以O(log3n) = O(C * log2n)，其中C=log32是一个常量。

即在采用 大**O**标记复杂度的时候，可以忽略系数，即**O(Cf(n)) = O(f(n))**。所以，O(log2n) 就等于O(log3n)。因此，在对数阶时间复杂度的表示方法里，我们忽略对数的“底”， 统一表示为O(logn) 
$$
log_32
$$



### 线性阶 O(n)

$$
线性阶 O(n)
$$

```
int cal(int n) {
   int sum = 0;
   int i = 1;
   for (; i<=n; ++i) {
       sum = sum + i;
   }
   return sum;
}
```

> 其中第2、3行代码都是常量级的执行时间，与n的大小无关，所以对于复杂度并没有影响
>
> 循环执行次数最多的是第4、5行代码，这两行代码被执行了n次，所以总的时间复杂度就是O(n) 



### 线性对数阶 O(nlogn)

$$
线性对数阶 O(nlogn)
$$

如果你理解了我前面讲的O(logn)，那O(nlogn)就很容易理解了。还记得我们刚讲的乘法法则吗?如果一段代码的时间复杂度是O(logn)，我们循环执行n遍，时间复 杂度就是O(nlogn)了。而且，O(nlogn)也是一种非常常见的算法时间复杂度

比如，归并排序、快速排序的时间复杂度都是O(nlogn) 



### 平方阶 O(n^2),  立方阶 O(n^3), ... k次方阶O(n^k)

$$
平方阶 O(n^2),  立方阶 O(n^3), ... k次方阶O(n^k)
$$

O(m+n), O(m*n)

```
int cal(int m, int n) {
   int sum_1 = 0;
   int i = 1;
   for (; i<m; ++i) {
       sum_1 = sum_1 + i;
   }

   int sum_2 = 0;
   int j = 1;
   for (; j<n; ++j) {
       sum_2 = sum_2 + j;
   }
   return sum_1 + sum_2;
}
```

> m和n是表示两个数据规模。无法事先评估m和n谁的量级大，所以在表示复杂度的时候，就不能简单地利用加法法则，省略掉其中一 个。所以，上面代码的时间复杂度就是O(m+n) 



## 非多项式量级 （低效）

当数据规模n越来越大时，非多项式量级算法的执行时间会急剧增加，求解问题的执行时间会无限增长 

非多项式时间复杂度的算法其实是非常低效的算法 

### 指数阶 O(2^n)

$$
指数阶 O(2^n)
$$

### 阶乘阶 O(n!)

$$
阶乘阶 O(n!)
$$

# 空间复杂度分析

空间复杂度全称就是渐进空间复杂度(asymptotic space complexity)，表示算法的存储空间与数据规模之间的增长关系 

常见的空间复杂度就是O(1)、O(n)、O(n2 )，像O(logn)、O(nlogn)这样的对数阶复杂度平时都用不到。

而且，空间复杂度分析比时间复杂度分析要简单很多。 所以，对于空间复杂度，掌握这些内容已经足够了 

## O(n)

```
void print(int n) {
    int i = 0;
    int[] a = new int[n];
    for (i; i<n; ++i) {
        a[i] = i * i;
    }
    for (i=n-1; i>=0; --i) {
        print out a[i]
    }
}
```

> 第2行代码中，我们申请了一个空间存储变量i，但是它是常量阶的，跟数据规模n没有关系，所以我们可以忽略。
>
> 第3行申请了一个大小为n的int类型数组，除此之外，剩下的代码都没有占用更多的空间，所以整段代码的空间复杂度就是O(n) 

# 复杂度分析

```
int find(int[] array, int n, int x) {
   int i = 0;
   int pos = -1;
   for (; i<n; ++i) {
       if (array[i] == x) {
           pos = i;
       }
   }
   return pos;
}
```

> 这段代码要实现的功能是，在一个无序的数组(array)中，查找变量x出现的位置。如果没有找到，就返回-1 
> 
> 这段代码的复杂度是O(n)，其中，n代表数组的长度 



在数组中查找一个数据，并不需要每次都把整个数组都遍历一遍，因为有可能中途找到就可以提前结束循环了。但是，这段代码写得不够高效。我们可以这样优化一下这段查找代码。

```
int find(int[] array, int n, int x) {
    int i = 0;
    int pos = -1;
    for (; i<n; ++i) {
        if (array[i] == x) {
            pos = i;
            break;
        }
    }
    return pos;
}
```

> 要查找的变量x可能出现在数组的任意位置。如果数组中第一个元素正好是要查找的变量x，那就不需要继续遍历剩下的n-1个数据了，那时间复杂度就 是O(1)。
>
> 但如果数组中不存在变量x，那我们就需要把整个数组都遍历一遍，时间复杂度就成了O(n)。
>
> 所以，不同的情况下，这段代码的时间复杂度是不一样的 



## 最好情况时间复杂度(best case time complexity)

最好情况时间复杂度就是，在最理想的情况下，执行这段代码的时间复杂度 

上述案例，在最理想的情况下，要查找的变量x正好是数组的第一个元素



## 最坏情况时间复杂度(worst case time complexity)

最坏情况时间复杂度就是，在最糟糕的情况下，执行这段代码的时间复杂度 

上述案例，如果数组中没有要查找的变量x，我们需要把整个数组 都遍历一遍才行，所以这种最糟糕情况下对应的时间复杂度就是最坏情况时间复杂度 



## 平均情况时间复杂度(average case time complexity)

要查找的变量x在数组中的位置，有n+1种情况:在数组的**0**~**n-1**位置中和不在数组中。我们把每种情况下，查找需要遍历的元素个数累加起来，然后再除以n+1， 就可以得到需要遍历的元素个数的平均值
$$
\frac{1+2+3+...+n+n}{n+1} = \frac{n(n+3)}{2(n+1)}
$$
时间复杂度的大O标记法中，可以省略掉系数、低阶、常量，所以，咱们把刚刚这个公式简化之后，得到的平均时间复杂度就是O(n) 

这个结论虽然是正确的，但是计算过程稍微有点儿问题。究竟是什么问题呢?我们刚讲的这n+1种情况，出现的概率并不是一样的 

我们知道，要查找的变量x，要么在数组里，要么就不在数组里。这两种情况对应的概率统计起来很麻烦，为了方便你理解，我们假设在数组中与不在数组中的概率都为1/2。另外，要查找的数据出现在0~n-1这n个位置的概率也是一样的，为1/n。所以，根据概率乘法法则，要查找的数据出现在0~n-1中任意位置的概率就 是1/(2n) 

因此，前面的推导过程中存在的最大问题就是，没有将各种情况发生的概率考虑进去。如果我们把每种情况发生的概率也考虑进去，那平均时间复杂度的计算过程就变成了这样
$$
1* \frac{1}{2n} + 2* \frac{1}{2n} + 3*\frac{1}{2n} + ... + n *\frac{1}{2n} + n*\frac{1}{2} = \frac{3n+1}{4}
$$
这个值就是概率论中的加权平均值，也叫作期望值，所以平均时间复杂度的全称应该叫加权平均时间复杂度或者期望时间复杂度。

引入概率之后，前面那段代码的加权平均值为(3n+1)/4。用大O表示法来表示，去掉系数和常量，这段代码的加权平均时间复杂度仍然是O(n)。 

你可能会说，平均时间复杂度分析好复杂啊，还要涉及概率论的知识。实际上，在大多数情况下，我们并不需要区分最好、最坏、平均情况时间复杂度三种情况。像我们上一节课举的那些例子那样，很多时候，我们使用一个复杂度就可以满足需求了。只有同一块代码在不同的情况下，时间复杂度有量级的差距，我们才会使用这三种复杂度表示法来区分



## 均摊时间复杂度(amortized time complexity)

大部分情况下，我们并不需要区分最好、最坏、平均三种复杂度。平均复杂度只在某些特殊情况下才会用到，而均摊时间复杂度应用的场景比它更加特殊、更加有限

```
int[] array = new int[n];
int count = 0;

void insert(int val) {
    if (count == array.length) {
        int sum = 0;
        for (int i=0;i<array.length; ++i) {
            sum = sum + array[i];
        }
        array[0] = sum;
        count = 1;
    }
    array[count] = val;
    ++count;
}
```

> 这段代码实现了一个往数组中插入数据的功能。当数组满了之后，也就是代码中的count == array.length时，我们用for循环遍历数组求和，并清空数组，将求和之后的sum值放到数组的第一个位置，然后再将新的数据插入。但如果数组一开始就有空闲空间，则直接将数据插入数组 
> 
> 最理想的情况下，数组中有空闲空间，我们只需要将数据插入到数组下标为count的位置就可以了，所以最好情况时间复杂度为O(1)。最坏的情况下，数组中没有 空闲空间了，我们需要先做一次数组的遍历求和，然后再将数据插入，所以最坏情况时间复杂度为O(n)。 
> 
> 平均时间复杂度是是O(1)

我们还可以通过前面讲的概率论的方法来分析。 

假设数组的长度是n，根据数据插入的位置的不同，我们可以分为n种情况，每种情况的时间复杂度是O(1)。除此之外，还有一种“额外”的情况，就是在数组没有空 闲空间时插入一个数据，这个时候的时间复杂度是O(n)。而且，这n+1种情况发生的概率一样，都是1/(n+1)。所以，根据加权平均的计算方法，我们求得的平均时 间复杂度就是 
$$
1*\frac{1}{n+1} + 1*\frac{1}{n+1} + ... + 1*\frac{1}{n+1} + n*\frac{1}{n+1} = O(i)
$$
每一次O(n)的插入操作，都会跟着n-1次O(1)的插入操作，所以把耗时多的那次操作均摊到接下来的n-1次耗时少的 操作上，均摊下来，这一组连续的操作的均摊时间复杂度就是O(1)。这就是均摊分析的大致思路 

对一个数据结构进行一组连续操作中，大部分情况下时间复杂度都很低，只有个别情况下时间复杂度比较高，而且这些操作之间存在前后连贯的时序关系，这个时候，我们就可以将这一组操作放在一块儿分析，看是否能将较高时间复杂度那次操作的耗时，平摊到其他那些时间复杂度比较低的操作上。而且，在能够应用均摊时间复杂度分析的场合，一般均摊时间复杂度就等于最好情况时间复杂度





# 线性表 Linear List

顾名思义，线性表就是数据排成像一条线一样的结构。每个线性表上的数据最多只有前和后两个方向。其实除了数组，链表、队 列、栈等也是线性表结构。 

# 非线性表 Non-Linear List

比如二叉树、堆、图等。之所以叫非线性，是因为，在非线性表中，数据之间并不是简单的前后关系





# Cheatsheet



## Common Data Structure Operations

| Data Structure                                               | Time Complexity Average | Time Complexity Average | Time Complexity Average | Time Complexity Average | Time Complexity Worst | Time Complexity Worst | Time Complexity Worst | Time Complexity Worst | Space Complexity Worst |
| :----------------------------------------------------------- | :---------------------- | :---------------------- | :---------------------- | :---------------------- | :-------------------- | :-------------------- | :-------------------- | :-------------------- | :--------------------- |
|                                                              | Access                  | Search                  | Insertion               | Deletion                | Access                | Search                | Insertion             | Deletion              |                        |
| [Array](http://en.wikipedia.org/wiki/Array_data_structure)   | `Θ(1)`                  | `Θ(n)`                  | `Θ(n)`                  | `Θ(n)`                  | `O(1)`                | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                 |
| [Stack](http://en.wikipedia.org/wiki/Stack_(abstract_data_type)) | `Θ(n)`                  | `Θ(n)`                  | `Θ(1)`                  | `Θ(1)`                  | `O(n)`                | `O(n)`                | `O(1)`                | `O(1)`                | `O(n)`                 |
| [Queue](http://en.wikipedia.org/wiki/Queue_(abstract_data_type)) | `Θ(n)`                  | `Θ(n)`                  | `Θ(1)`                  | `Θ(1)`                  | `O(n)`                | `O(n)`                | `O(1)`                | `O(1)`                | `O(n)`                 |
| [Singly-Linked List](http://en.wikipedia.org/wiki/Singly_linked_list#Singly_linked_lists) | `Θ(n)`                  | `Θ(n)`                  | `Θ(1)`                  | `Θ(1)`                  | `O(n)`                | `O(n)`                | `O(1)`                | `O(1)`                | `O(n)`                 |
| [Doubly-Linked List](http://en.wikipedia.org/wiki/Doubly_linked_list) | `Θ(n)`                  | `Θ(n)`                  | `Θ(1)`                  | `Θ(1)`                  | `O(n)`                | `O(n)`                | `O(1)`                | `O(1)`                | `O(n)`                 |
| [Skip List](http://en.wikipedia.org/wiki/Skip_list)          | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                | `O(n log(n))`          |
| [Hash Table](http://en.wikipedia.org/wiki/Hash_table)        | `N/A`                   | `Θ(1)`                  | `Θ(1)`                  | `Θ(1)`                  | `N/A`                 | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                 |
| [Binary Search Tree](http://en.wikipedia.org/wiki/Binary_search_tree) | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                 |
| [Cartesian Tree](https://en.wikipedia.org/wiki/Cartesian_tree) | `N/A`                   | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `N/A`                 | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                 |
| [B-Tree](http://en.wikipedia.org/wiki/B_tree)                | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(n)`                 |
| [Red-Black Tree](http://en.wikipedia.org/wiki/Red-black_tree) | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(n)`                 |
| [Splay Tree](https://en.wikipedia.org/wiki/Splay_tree)       | `N/A`                   | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `N/A`                 | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(n)`                 |
| [AVL Tree](http://en.wikipedia.org/wiki/AVL_tree)            | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(log(n))`           | `O(n)`                 |
| [KD Tree](http://en.wikipedia.org/wiki/K-d_tree)             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `Θ(log(n))`             | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                | `O(n)`                 |





## Array Sorting Algorithms

| Algorithm                                                    | Time Complexity | Time Complexity  | Time Complexity  | Space Complexity |
| :----------------------------------------------------------- | :-------------- | :--------------- | :--------------- | :--------------- |
|                                                              | Best            | Average          | Worst            | Worst            |
| [Quicksort](http://en.wikipedia.org/wiki/Quicksort)          | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n^2)`         | `O(log(n))`      |
| [Mergesort](http://en.wikipedia.org/wiki/Merge_sort)         | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           |
| [Timsort](http://en.wikipedia.org/wiki/Timsort)              | `Ω(n)`          | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           |
| [Heapsort](http://en.wikipedia.org/wiki/Heapsort)            | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n log(n))`    | `O(1)`           |
| [Bubble Sort](http://en.wikipedia.org/wiki/Bubble_sort)      | `Ω(n)`          | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           |
| [Insertion Sort](http://en.wikipedia.org/wiki/Insertion_sort) | `Ω(n)`          | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           |
| [Selection Sort](http://en.wikipedia.org/wiki/Selection_sort) | `Ω(n^2)`        | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           |
| [Tree Sort](https://en.wikipedia.org/wiki/Tree_sort)         | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n^2)`         | `O(n)`           |
| [Shell Sort](http://en.wikipedia.org/wiki/Shellsort)         | `Ω(n log(n))`   | `Θ(n(log(n))^2)` | `O(n(log(n))^2)` | `O(1)`           |
| [Bucket Sort](http://en.wikipedia.org/wiki/Bucket_sort)      | `Ω(n+k)`        | `Θ(n+k)`         | `O(n^2)`         | `O(n)`           |
| [Radix Sort](http://en.wikipedia.org/wiki/Radix_sort)        | `Ω(nk)`         | `Θ(nk)`          | `O(nk)`          | `O(n+k)`         |
| [Counting Sort](https://en.wikipedia.org/wiki/Counting_sort) | `Ω(n+k)`        | `Θ(n+k)`         | `O(n+k)`         | `O(k)`           |
| [Cubesort](https://en.wikipedia.org/wiki/Cubesort)           | `Ω(n)`          | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           |

# Appendix

https://time.geekbang.org/column/article/40036

https://www.bigocheatsheet.com/

https://www.geeksforgeeks.org/

