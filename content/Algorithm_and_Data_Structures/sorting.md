---
title: "sorting"
date: 2021-02-25 19:27
---
[toc]



# 排序



对于排序算法执行效率的分析，我们一般会从这几个方面来衡量



## 排序分析



**最好情况、最坏情况、平均情况时间复杂度**

我们在分析排序算法的时间复杂度时，要分别给出最好情况、最坏情况、平均情况下的时间复杂度。除此之外，你还要说出最好、最坏时间复杂度对应的要排序的原始数据是什么样的。

为什么要区分这三种时间复杂度呢？

第一，有些排序算法会区分，为了好对比，所以我们最好都做一下区分。

第二，对于要排序的数据，有的接近有序，有的完全无序。有序度不同的数据，对于排序的执行时间肯定是有影响的，我们要知道排序算法在不同数据下的性能表现。



**时间复杂度的系数、常数 、低阶**

我们知道，时间复杂度反映的是数据规模 n 很大的时候的一个增长趋势，所以它表示的时候会忽略系数、常数、低阶。

但是实际的软件开发中，我们排序的可能是 10 个、100 个、1000 个这样规模很小的数据，所以，在对同一阶时间复杂度的排序算法性能对比的时候，我们就要把系数、常数、低阶也考虑进来。



**比较次数和交换（或移动）次数**

基于比较的排序算法的执行过程，会涉及两种操作，一种是元素比较大小，另一种是元素交换或移动。

所以，如果我们在分析排序算法的执行效率的时候，应该把比较次数和交换（或移动）次数也考虑进去。





如果对小规模数据进行排序，可以选择时间复杂度是 O(n2) 的算法；如果对大规模数据进行排序，时间复杂度是 O(nlogn) 的算法更加高效。所以，为了兼顾任意规模数据的排序，一般都会首选时间复杂度是 O(nlogn) 的排序算法来实现排序函数。



## 排序算法的内存消耗

我们前面讲过，算法的内存消耗可以通过空间复杂度来衡量，排序算法也不例外

不过，针对排序算法的空间复杂度，我们还引入了一个新的概念，原地排序（Sorted in place）。原地排序算法，就是特指空间复杂度是 O(1) 的排序算法

如heapsort, bubble sort, insertion sort, selection sort, shell sort



## 排序算法的稳定性

仅仅用执行效率和内存消耗来衡量排序算法的好坏是不够的。针对排序算法，我们还有一个重要的度量指标，稳定性。

这个概念是说，如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变。



比如我们有一组数据 2，9，3，4，8，3，按照大小排序之后就是 2，3，3，4，8，9。这组数据里有两个 3。经过某种排序算法排序之后，如果两个 3 的前后顺序没有改变，那我们就把这种排序算法叫作稳定的排序算法；如果前后顺序发生变化，那对应的排序算法就叫作不稳定的排序算法。





### 稳定的排序算法

比如说，我们现在要给电商交易系统中的“订单”排序。

订单有两个属性，一个是下单时间，另一个是订单金额。如果我们现在有 10 万条订单数据，我们希望按照金额从小到大对订单数据排序。对于金额相同的订单，我们希望按照下单时间从早到晚有序。对于这样一个排序需求，我们怎么来做呢？

最先想到的方法是：我们先按照金额对订单数据进行排序，然后，再遍历排序之后的订单数据，对于每个金额相同的小区间再按照下单时间排序。这种排序思路理解起来不难，但是实现起来会很复杂。

借助稳定排序算法，这个问题可以非常简洁地解决。

解决思路是这样的：我们先按照下单时间给订单排序，注意是按照下单时间，不是金额。

排序完成之后，我们用稳定排序算法，按照订单金额重新排序。

两遍排序之后，我们得到的订单数据就是按照金额从小到大排序，金额相同的订单按照下单时间从早到晚排序的。为什么呢？

稳定排序算法可以保持金额相同的两个对象，在排序之后的前后顺序不变。

第一次排序之后，所有的订单按照下单时间从早到晚有序了。

在第二次排序中，我们用的是稳定的排序算法，所以经过第二次排序之后，相同金额的订单仍然保持下单时间从早到晚有序。



# 冒泡排序 Bubble Sort

冒泡排序只会操作相邻的两个数据。每次冒泡操作都会对相邻的两个元素进行比较，看是否满足大小关系要求。如果不满足就让它俩互换。一次冒泡会让至少一个元素移动到它应该在的位置，重复 n 次，就完成了 n 个数据的排序工作。

这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端



![image-20210225210213172](sorting.assets/image-20210225210213172.png)



## 特点

**原地排序算法**

冒泡的过程只涉及相邻数据的交换操作，只需要常量级的临时空间，所以它的空间复杂度为 O(1)



**稳定的排序算法**

在冒泡排序中，只有交换才可以改变两个元素的前后顺序。为了保证冒泡排序算法的稳定性，当有相邻的两个元素大小相等的时候，我们不做交换，相同大小的数据在排序前后不会改变顺序



**复杂度**

最好情况下，要排序的数据已经是有序的了，我们只需要进行一次冒泡操作，就可以结束了，所以最好情况时间复杂度是 O(n)。

而最坏的情况是，要排序的数据刚好是倒序排列的，我们需要进行 n 次冒泡操作，所以最坏情况时间复杂度为 O(n^2)。

平均复杂度为O(n^2)



## Java

```

// 冒泡排序，a表示数组，n表示数组大小
public void bubbleSort(int[] a, int n) {
  if (n <= 1) return;
 
 for (int i = 0; i < n; ++i) {
    // 提前退出冒泡循环的标志位
    boolean flag = false;
    for (int j = 0; j < n - i - 1; ++j) {
      if (a[j] > a[j+1]) { // 交换
        int tmp = a[j];
        a[j] = a[j+1];
        a[j+1] = tmp;
        flag = true;  // 表示有数据交换      
      }
    }
    if (!flag) break;  // 没有数据交换，提前退出
  }
}
```



## Python

```
class BubbleSort:

    def __init__(self, array: list):
        self._array = array
        self._length = len(self._array)

    def sort(self):
        if self._length <= 1:
            return
        for i in range(self._length):
            flag = False
            for j in range(self._length - i - 1):
                if self._array[j] > self._array[j+1]:
                    self._array[j], self._array[j+1] = self._array[j+1], self._array[j]
                    flag = True
            if not flag:
                break
        return self._array


if __name__ == '__main__':
    my_array = BubbleSort([3, 5, 1, 8, 7])
    print(my_array.sort())
```

> ```
> >>>
> [1, 3, 5, 7, 8]
> ```
>
> 





# 插入排序 Insertion Sort 

插入排序也包含两种操作，一种是元素的比较，一种是元素的移动。

当我们需要将一个数据 a 插入到已排序区间时，需要拿 a 与已排序区间的元素依次比较大小，找到合适的插入位置。

找到插入点之后，我们还需要将插入点之后的元素顺序往后移动一位，这样才能腾出位置给元素 a 插入。

对于不同的查找插入点方法（从头到尾、从尾到头），元素的比较次数是有区别的。但对于一个给定的初始序列，移动操作的次数总是固定的，就等于逆序度。

![image-20210225225106648](sorting.assets/image-20210225225106648.png)





## 特点

**原地排序算法**

从实现过程可以很明显地看出，插入排序算法的运行并不需要额外的存储空间，所以空间复杂度是 O(1)



**稳定的排序算法**

在插入排序中，对于值相同的元素，我们可以选择将后面出现的元素，插入到前面出现元素的后面，这样就可以保持原有的前后顺序不变





**复杂度**

如果要排序的数据已经是有序的，我们并不需要搬移任何数据。如果我们从尾到头在有序数据组里面查找插入位置，每次只需要比较一个数据就能确定插入的位置。所以这种情况下，最好是时间复杂度为 O(n)

注意，这里是从尾到头遍历已经有序的数据。如果数组是倒序的，每次插入都相当于在数组的第一个位置插入新的数据，所以需要移动大量的数据，所以最坏情况时间复杂度为 O(n^2)

还记得我们在数组中插入一个数据的平均时间复杂度是多少吗？没错，是 O(n)。所以，对于插入排序来说，每次插入操作都相当于在数组中插入一个数据，循环执行 n 次插入操作，所以平均时间复杂度为 O(n^2)





## Java

```
// 插入排序，a表示数组，n表示数组大小
public void insertionSort(int[] a, int n) {
  if (n <= 1) return;

  for (int i = 1; i < n; ++i) {
    int value = a[i];
    int j = i - 1;
    // 查找插入的位置
    for (; j >= 0; --j) {
      if (a[j] > value) {
        a[j+1] = a[j];  // 数据移动
      } else {
        break;
      }
    }
    a[j+1] = value; // 插入数据
  }
}
```





## Python

```
class InsertionSort:

    def __init__(self, array: list):
        self._array = array
        self._length = len(self._array)

    def sort(self):
        if self._length <= 1:
            return

        for i in range(1, self._length):
            get_swap_value = self._array[i]
            position = i - 1
            while position >= 0 and self._array[position] > get_swap_value:
                self._array[position+1] = self._array[position]
                position -= 1
            self._array[position+1] = get_swap_value

        return self._array


if __name__ == '__main__':
    my_array = InsertionSort([1, 4, 7, 2, 3, 8])
    print(my_array.sort())
    
```

> ```
> >>>
> [1, 2, 3, 4, 7, 8]
> ```
>
> 



# 冒泡 vs 插入

冒泡排序不管怎么优化，元素交换的次数是一个固定值，是原始数据的逆序度。插入排序是同样的，不管怎么优化，元素移动的次数也等于原始数据的逆序度。

但是，从代码实现上来看，冒泡排序的数据交换要比插入排序的数据移动要复杂，冒泡排序需要 3 个赋值操作，而插入排序只需要 1 个

```
冒泡排序中数据的交换操作：
if (a[j] > a[j+1]) { // 交换
   int tmp = a[j];
   a[j] = a[j+1];
   a[j+1] = tmp;
   flag = true;
}

插入排序中数据的移动操作：
if (a[j] > value) {
  a[j+1] = a[j];  // 数据移动
} else {
  break;
}
```



我们把执行一个赋值语句的时间粗略地计为单位时间（unit_time），然后分别用冒泡排序和插入排序对同一个逆序度是 K 的数组进行排序。用冒泡排序，需要 K 次交换操作，每次需要 3 个赋值语句，所以交换操作总耗时就是 3*K 单位时间。而插入排序中数据移动操作只需要 K 个单位时间。

# 选择排序 Selection Sort

选择排序算法的实现思路有点类似插入排序，也分已排序区间和未排序区间

但是选择排序每次会从未排序区间中找到最小的元素，将其放到已排序区间的末尾。



## 特点

选择排序空间复杂度为 O(1)，是一种**原地排序算法**

选择排序的最好情况时间复杂度、最坏情况和平均情况时间复杂度都为 O(n^2)

选择排序是一种**不稳定的排序算法**。从我前面画的那张图中，你可以看出来，选择排序每次都要找剩余未排序元素中的最小值，并和前面的元素交换位置，这样破坏了稳定性。

比如 5，8，5，2，9 这样一组数据，使用选择排序算法来排序的话，第一次找到最小元素 2，与第一个 5 交换位置，那第一个 5 和中间的 5 顺序就变了，所以就不稳定了。正是因此，相对于冒泡排序和插入排序，选择排序就稍微逊色了。







## Python

```
class SelectionSort:
    def __init__(self, array: []):
        self._array = array
        self._length = len(self._array)

    def sort(self):
        if self._length <= 1:
            return

        for i in range(self._length):
            min_index = i
            min_value = self._array[i]
            for j in range(i, self._length):
                if self._array[j] < min_value:
                    min_value = self._array[j]
                    min_index = j
            self._array[i], self._array[min_index] = self._array[min_index], self._array[i]  # swap min_value at beginning

        return self._array


if __name__ == '__main__':
    my_array = SelectionSort([4, 7, 3, 8])
    print(my_array.sort())

```

> ```
> >>>
> [3, 4, 7, 8]
> ```
>
> 



# 归并排序 Merge Sort

如果要排序一个数组，我们先把数组从中间分成前后两部分，然后对前后两部分分别排序，再将排好序的两部分合并在一起，这样整个数组就都有序了。

![image-20210228130549345](sorting.assets/image-20210228130549345.png)



归并排序用的是分治思想，可以用递归来实现。

写递归代码的技巧就是，分析得出递推公式，然后找到终止条件，最后将递推公式翻译成递归代码。所以，要想写出归并排序的代码，我们先写出归并排序的递推公式。

```
递推公式：
merge_sort(p…r) = merge(merge_sort(p…q), merge_sort(q+1…r))

终止条件：
p >= r 不用再继续分解
```

> 其中下标 q 等于 p 和 r 的中间位置，也就是 (p+r)/2。当下标从 p 到 q 和从 q+1 到 r 这两个子数组都排好序之后，我们再将两个有序的子数组合并在一起，这样下标从 p 到 r 之间的数据就也排好序了。



## 特点



**稳定的排序算法**

归并排序稳不稳定关键要看 merge() 函数，也就是两个有序子数组合并成一个有序数组的那部分代码。在合并的过程中，如果 A[p...q]和 A[q+1...r]之间有值相同的元素，那我们可以像伪代码中那样，先把 A[p...q]中的元素放入 tmp 数组。这样就保证了值相同的元素，在合并前后的先后顺序不变



归并排序涉及递归，时间复杂度的分析稍微有点复杂。我们正好借此机会来学习一下，如何分析递归代码的时间复杂度。在递归那一节我们讲过，递归的适用场景是，一个问题 a 可以分解为多个子问题 b、c，那求解问题 a 就可以分解为求解问题 b、c。问题 b、c 解决之后，我们再把 b、c 的结果合并成 a 的结果。如果我们定义求解问题 a 的时间是 T(a)，求解问题 b、c 的时间分别是 T(b) 和 T( c)，那我们就可以得到这样的递推关系式：

```
T(a) = T(b) + T(c) + K
```

其中 K 等于将两个子问题 b、c 的结果合并成问题 a 的结果所消耗的时间。从刚刚的分析，我们可以得到一个重要的结论：不仅递归求解的问题可以写成递推公式，递归代码的时间复杂度也可以写成递推公式。





我们假设对 n 个元素进行归并排序需要的时间是 T(n)，那分解成两个子数组排序的时间都是 T(n/2)。我们知道，merge() 函数合并两个有序子数组的时间复杂度是 O(n)。所以，套用前面的公式，归并排序的时间复杂度的计算公式就是：

```
T(1) = C； n=1时，只需要常量级的执行时间，所以表示为C
T(n) = 2*T(n/2) + n； n>1
```

通过这个公式，如何来求解 T(n) 呢？还不够直观？那我们再进一步分解一下计算过程。

```
T(n) = 2*T(n/2) + n
     = 2*(2*T(n/4) + n/2) + n = 4*T(n/4) + 2*n
     = 4*(2*T(n/8) + n/4) + 2*n = 8*T(n/8) + 3*n
     = 8*(2*T(n/16) + n/8) + 3*n = 16*T(n/16) + 4*n
     ......
     = 2^k * T(n/2^k) + k * n
     ......
```



通过这样一步一步分解推导，我们可以得到 T(n) = 2^kT(n/2^k)+kn。当 T(n/2^k)=T(1) 时，也就是 n/2^k=1，我们得到 k=log2n 。我们将 k 值代入上面的公式，得到 T(n)=Cn+nlog2n 。如果我们用大 O 标记法来表示的话，T(n) 就等于 O(nlogn)。所以归并排序的时间复杂度是 O(nlogn)。

从我们的原理分析和伪代码可以看出，归并排序的执行效率与要排序的原始数组的有序程度无关，所以其时间复杂度是非常稳定的，不管是最好情况、最坏情况，还是平均情况，时间复杂度都是 O(nlogn)。





归并排序的时间复杂度任何情况下都是 O(nlogn)，看起来非常优秀。（待会儿你会发现，即便是快速排序，最坏情况下，时间复杂度也是 O(n2)。）但是，归并排序并没有像快排那样，应用广泛，这是为什么呢？因为它有一个致命的“弱点”，那就是归并排序**不是原地排序算法**

递归代码的空间复杂度并不能像时间复杂度那样累加。刚刚我们忘记了最重要的一点，那就是，尽管每次合并操作都需要申请额外的内存空间，但在合并完成之后，临时开辟的内存空间就被释放掉了。在任意时刻，CPU 只会有一个函数在执行，也就只会有一个临时的内存空间在使用。临时内存空间最大也不会超过 n 个数据的大小，所以空间复杂度是 O(n)。



## Python

```
from typing import List


class MergeSort:

    @staticmethod
    def sort(array: List[int]):
        MergeSort.merge_sort_between(array, 0, len(array)-1)

    @staticmethod
    def merge_sort_between(array: List[int], low: int, high: int):
        if low < high:
            middle = low + (high - low) // 2
            MergeSort.merge_sort_between(array, low, middle)
            MergeSort.merge_sort_between(array, middle+1, high)
            MergeSort.merge(array, low, middle, high)

    @staticmethod
    def merge(array: List[int], low: int, middle: int, high: int):
        i, j = low, middle+1
        tmp_list = []
        while i <= middle and j <= high:
            if array[i] <= array[j]:
                tmp_list.append(array[i])
                i += 1
            else:
                tmp_list.append(array[j])
                j += 1

        start = i if i <= middle else j
        end = middle if i <= middle else high
        tmp_list.extend(array[start: end+1])
        array[low: high+1] = tmp_list
        return tmp_list


if __name__ == '__main__':
    my_array = [6, 5, 12, 10, 9, 1]
    MergeSort.sort(my_array)
    print(my_array)

```

> ```
> >>>
> [1, 5, 6, 9, 10, 12]
> ```
>
> 







# 快速排序 Quick Sort （常用）

快排的思想是这样的：如果要排序数组中下标从 p 到 r 之间的一组数据，我们选择 p 到 r 之间的任意一个数据作为 pivot（分区点）。

我们遍历 p 到 r 之间的数据，将小于 pivot 的放到左边，将大于 pivot 的放到右边，将 pivot 放到中间。

经过这一步骤之后，数组 p 到 r 之间的数据就被分成了三个部分，前面 p 到 q-1 之间都是小于 pivot 的，中间是 pivot，后面的 q+1 到 r 之间是大于 pivot 的。

![image-20210304155754834](sorting.assets/image-20210304155754834.png)





如果我们用递推公式来将上面的过程写出来的话，就是这样：

```
quick_sort(p…r) = quick_sort(p…q-1) + quick_sort(q+1… r)

终止条件：
p >= r
```



我们将递推公式转化成递归代码。跟归并排序一样，我还是用伪代码来实现，你可以翻译成你熟悉的任何语言。

```
// 快速排序，A是数组，n表示数组的大小
quick_sort(A, n) {
  quick_sort_c(A, 0, n-1)
}
// 快速排序递归函数，p,r为下标
quick_sort_c(A, p, r) {
  if p >= r then return
  
  q = partition(A, p, r) // 获取分区点
  quick_sort_c(A, p, q-1)
  quick_sort_c(A, q+1, r)
}
```





## 特点

快排是一种原地、不稳定的排序算法。

快排也是用递归来实现的。对于递归代码的时间复杂度，我前面总结的公式，这里也还是适用的。如果每次分区操作，都能正好把数组分成大小接近相等的两个小区间，那快排的时间复杂度递推求解公式跟归并是相同的。所以，快排的时间复杂度也是 O(nlogn)。

```
T(1) = C；   n=1时，只需要常量级的执行时间，所以表示为C。
T(n) = 2*T(n/2) + n； n>1
```





## 优化分析

如果数据原来就是有序的或者接近有序的，每次分区点都选择最后一个数据，那快速排序算法就会变得非常糟糕，时间复杂度就会退化为 O(n2)。实际上，这种 O(n2) 时间复杂度出现的主要原因还是因为我们分区点选得不够合理。



最理想的分区点是：被分区点分开的两个分区中，数据的数量差不多。

如果很粗暴地直接选择第一个或者最后一个数据作为分区点，不考虑数据的特点，肯定会出现之前讲的那样，在某些情况下，排序的最坏情况时间复杂度是 O(n2)。为了提高排序算法的性能，我们也要尽可能地让每次分区都比较平均。

这里介绍两个比较常用、比较简单的分区算法，你可以直观地感受一下。



1. 三数取中法我们从区间的首、尾、中间，分别取出一个数，然后对比大小，取这 3 个数的中间值作为分区点。这样每间隔某个固定的长度，取数据出来比较，将中间值作为分区点的分区算法，肯定要比单纯取某一个数据更好。但是，如果要排序的数组比较大，那“三数取中”可能就不够了，可能要“五数取中”或者“十数取中”。
2. 随机法随机法就是每次从要排序的区间中，随机选择一个元素作为分区点。这种方法并不能保证每次分区点都选的比较好，但是从概率的角度来看，也不大可能会出现每次分区点都选得很差的情况，所以平均情况下，这样选的分区点是比较好的。时间复杂度退化为最糟糕的 O(n2) 的情况，出现的可能性不大。



我们知道，快速排序是用递归来实现的。我们在递归那一节讲过，递归要警惕堆栈溢出。为了避免快速排序里，递归过深而堆栈过小，导致堆栈溢出，我们有两种解决办法：第一种是限制递归深度。一旦递归过深，超过了我们事先设定的阈值，就停止递归。第二种是通过在堆上模拟实现一个函数调用栈，手动模拟递归压栈、出栈的过程，这样就没有了系统栈大小的限制。





## Python

```
from typing import List
import random


class QuickSort:

    @staticmethod
    def sort(array: List[int]):
        QuickSort.quick_sort_between(array, 0, len(array) - 1)

    @staticmethod
    def quick_sort_between(array: List[int], low: int, high: int):
        if low < high:
            k = random.randint(low, high)
            array[low], array[k] = array[k], array[low]

            m = QuickSort.partition(array, low, high)
            QuickSort.quick_sort_between(array, low, m-1)
            QuickSort.quick_sort_between(array, m+1, high)

    @staticmethod
    def partition(array: List[int], low: int, high: int):
        pivot, j = array[low], low
        for i in range(low+1, high+1):
            if array[i] <= pivot:
                j += 1
                array[j], array[i] = array[i], array[j]
        array[low], array[j] = array[j], array[low]
        return j


if __name__ == '__main__':
    my_array = [6, 5, 12, 10, 9, 1]
    QuickSort.sort(my_array)
    print(my_array)
```

> ```
> >>>
> [1, 5, 6, 9, 10, 12]
> ```
>
> 



### two-way 双向排序

提高非随机输入的性能

不需要额外的空间,在待排序数组本身内部进行排序

基准值通过random随机选取









## Question

现在你有 10 个接口访问日志文件，每个日志文件大小约 300MB，每个文件里的日志都是按照时间戳从小到大排序的。你希望将这 10 个较小的日志文件，合并为 1 个日志文件，合并之后的日志仍然按照时间戳从小到大排列。如果处理上述排序任务的机器内存只有 1GB，你有什么好的解决思路，能“快速”地将这 10 个日志文件合并吗？



先构建十条io流，分别指向十个文件，每条io流读取对应文件的第一条数据，然后比较时间戳，选择出时间戳最小的那条数据，将其写入一个新的文件，然后指向该时间戳的io流读取下一行数据，然后继续刚才的操作，比较选出最小的时间戳数据，写入新文件，io流读取下一行数据，以此类推，完成文件的合并， 这种处理方式，日志文件有n个数据就要比较n次，每次比较选出一条数据来写入，时间复杂度是O（n），空间复杂度是O（1）,几乎不占用内存

# 归并 vs 快排

归并排序的处理过程是由下到上的，先处理子问题，然后再合并。而快排正好相反，它的处理过程是由上到下的，先分区，然后再处理子问题。

归并排序虽然是稳定的、时间复杂度为 O(nlogn) 的排序算法，但是它是非原地排序算法。我们前面讲过，归并之所以是非原地排序算法，主要原因是合并函数无法在原地执行。

快速排序通过设计巧妙的原地分区函数，可以实现原地排序，解决了归并排序占用太多内存的问题。



快速排序算法虽然最坏情况下的时间复杂度是 O(n2)，但是平均情况下时间复杂度都是 O(nlogn)。不仅如此，快速排序算法时间复杂度退化到 O(n2) 的概率非常小，我们可以通过合理地选择 pivot 来避免这种情况。







# 桶排序 Bucket Sort

核心思想是将要排序的数据分到几个有序的桶里，每个桶里的数据再单独进行排序。桶内排完序之后，再把每个桶里的数据按照顺序依次取出，组成的序列就是有序的了。

![image-20210311085257387](sorting.assets/image-20210311085257387.png)

桶排序的时间复杂度为什么是 O(n) 呢？我们一块儿来分析一下。

如果要排序的数据有 n 个，我们把它们均匀地划分到 m 个桶内，每个桶里就有 k=n/m 个元素。每个桶内部使用快速排序，时间复杂度为 O(k * logk)。m 个桶排序的时间复杂度就是 O(m * k * logk)，因为 k=n/m，所以整个桶排序的时间复杂度就是 O(n*log(n/m))。当桶的个数 m 接近数据个数 n 时，log(n/m) 就是一个非常小的常量，这个时候桶排序的时间复杂度接近 O(n)。



## 劣势

桶排序对要排序数据的要求是非常苛刻的

首先，要排序的数据需要很容易就能划分成 m 个桶，并且，桶与桶之间有着天然的大小顺序。这样每个桶内的数据都排序完之后，桶与桶之间的数据不需要再进行排序。

其次，数据在各个桶之间的分布是比较均匀的。如果数据经过桶的划分之后，有些桶里的数据非常多，有些非常少，很不平均，那桶内数据排序的时间复杂度就不是常量级了。在极端情况下，如果数据都被划分到一个桶里，那就退化为 O(nlogn) 的排序算法了。

要排序的数据都有比较苛刻的要求，应用不是非常广泛。但是如果数据特征比较符合其要求，线性的时间复杂度可以达到O(n)



## 优势

桶排序比较适合用在外部排序中。所谓的外部排序就是数据存储在外部磁盘中，数据量比较大，内存有限，无法将数据全部加载到内存中。

比如说我们有 10GB 的订单数据，我们希望按订单金额（假设金额都是正整数）进行排序，但是我们的内存有限，只有几百 MB，没办法一次性把 10GB 的数据都加载到内存中。这个时候该怎么办呢？

我们可以先扫描一遍文件，看订单金额所处的数据范围。假设经过扫描之后我们得到，订单金额最小是 1 元，最大是 10 万元。我们将所有订单根据金额划分到 100 个桶里，第一个桶我们存储金额在 1 元到 1000 元之内的订单，第二桶存储金额在 1001 元到 2000 元之内的订单，以此类推。每一个桶对应一个文件，并且按照金额范围的大小顺序编号命名（00，01，02...99）。

理想的情况下，如果订单金额在 1 到 10 万之间均匀分布，那订单会被均匀划分到 100 个文件中，每个小文件中存储大约 100MB 的订单数据，我们就可以将这 100 个小文件依次放到内存中，用快排来排序。等所有文件都排好序之后，我们只需要按照文件编号，从小到大依次读取每个小文件中的订单数据，并将其写入到一个文件中，那这个文件中存储的就是按照金额从小到大排序的订单数据了。



## 特点

桶排序和计数排序的排序思想是非常相似的，都是针对范围不大的数据，将数据划分成不同的桶来实现排序。

**稳定排序算法**， 不是 **原地排序算法**



## Python

```
from typing import List
import math


class BucketSort:

    def __init__(self, array: List[int], bucket_size=None):
        self._array = array
        self._length = len(self._array)
        self._bucket_size = bucket_size if bucket_size else 5

    def sort(self):
        if self._length < 2:
            return

        unconcat_bucket = self.create_bucket()
        sorted_bucket_values = self.sort_bucket_dict(unconcat_bucket)
        return sorted_bucket_values

    def create_bucket(self):
        min_value = self._array[0]
        max_value = self._array[1]
        for i in range(len(self._array)):
            if self._array[i] < min_value:
                min_value = self._array[i]
            if self._array[i] > max_value:
                max_value = self._array[i]

        bucket_count_dict = {}
        bucket_count = math.floor((max_value - min_value) / self._bucket_size) + 1

        # generate buckets with index id
        for i in range(bucket_count):
            bucket_count_dict[str(i)] = []

        # put value into their corresponding bucket
        for i in range(len(self._array)):
            bucket_index = math.floor((self._array[i] - min_value) / self._bucket_size)
            bucket_count_dict[str(bucket_index)].append(self._array[i])

        # sorting value in the same bucket, using quick sort
        for i in range(bucket_count-1):
            BucketSort.quick_sort(bucket_count_dict[str(i)], 0, len(bucket_count_dict[str(i)])-1)

        return bucket_count_dict

    def sort_bucket_dict(self, unconcat_bucket: dict):
        sorted_list = []
        for i in range(len(unconcat_bucket)):
            for j in unconcat_bucket[str(i)]:
                sorted_list.append(j)
        return sorted_list

    @staticmethod
    def quick_sort(array: List[int], low: int, high: int):
        if low >= high:
            return

        get_partition_value = BucketSort.partition(array, low, high)
        BucketSort.quick_sort(array, low, get_partition_value-1)
        BucketSort.quick_sort(array, get_partition_value+1, high)

    @staticmethod
    def partition(array: List[int], low: int, high: int):
        pivot, j = array[low], low
        for i in range(low+1, high+1):
            if array[i] <= pivot:
                j += 1
                array[j], array[i] = array[i], array[j]
        array[low], array[j] = array[j], array[low]
        return j


if __name__ == '__main__':
    my_array = [11, 9, 21, 8, 17, 19, 13, 1, 24, 12]
    sorted_my_array = BucketSort(my_array).sort()
    print(sorted_my_array)

```

> ```
> >>>
> [1, 8, 9, 11, 12, 13, 17, 19, 21, 24]
> ```
>
> 



# 计数排序 Counting sort

计数排序其实是桶排序的一种特殊情况

1. 当要排序的 8 个数据，` [2, 5, 3, 0, 2, 3, 0, 3]`，所处的范围并不大的时候，这里最大值是 5，初始化 **最大值+1** 个桶的数组，全部元素为0, 每个桶内存储对应的值，而且数据值都是相同的，省掉了桶内排序的时间
2. 整个数组中，相同元素的计数统计值，找到其在对应的下标中的位置，如数组中元素3共有3个，将3存储在下标为3的位置上，不存在则存储为0，以此得到 `[2, 0, 2, 3, 0, 1]` 记为数组A
3. 数组A内依次累加数组元素，并存储到相应下标位置，得到`[2, 2, 4, 7, 7, 8]`，记为数组B
4. 从后往前(对比从前往后更稳定) ，将源数组中的元素，找到其下标所在的，数组B的值，减去1得到排序位置下标值，如源数组倒数第一个3，在累加数组B下标数组的值为7，减去1得6，即排序后的位置在第6位。 因为数组B已经找到对应的值，这里需要剪去，得到新的B `[2, 2, 4, 6, 7, 8]`

![image-20210321001751572](sorting.assets/image-20210321001751572.png)



![image-20210321001819137](sorting.assets/image-20210321001819137.png)

![image-20210321001834102](sorting.assets/image-20210321001834102.png)



![image-20210321001853871](sorting.assets/image-20210321001853871.png)



![image-20210321001904160](sorting.assets/image-20210321001904160.png)







计数排序的算法思想就是这么简单，跟桶排序非常类似，只是桶的大小粒度不一样。





## 特点

计数排序只能用在数据范围不大的场景中，如果数据范围 k 比要排序的数据 n 大很多，就不适合用计数排序了。而且，计数排序只能给非负整数排序，如果要排序的数据是其他类型的，要将其在不改变相对大小的情况下，转化为非负整数。

**稳定排序算法**， 但是 **不是原地排序算法**





## 劣势

要排序的数据都有比较苛刻的要求，应用不是非常广泛。但是如果数据特征比较符合其要求，线性的时间复杂度可以达到O(n)



## Java

```
// 计数排序，a是数组，n是数组大小。假设数组中存储的都是非负整数。
public void countingSort(int[] a, int n) {
  if (n <= 1) return;

  // 查找数组中数据的范围
  int max = a[0];
  for (int i = 1; i < n; ++i) {
    if (max < a[i]) {
      max = a[i];
    }
  }

  int[] c = new int[max + 1]; // 申请一个计数数组c，下标大小[0,max]
  for (int i = 0; i <= max; ++i) {
    c[i] = 0;
  }

  // 计算每个元素的个数，放入c中
  for (int i = 0; i < n; ++i) {
    c[a[i]]++;
  }

  // 依次累加
  for (int i = 1; i <= max; ++i) {
    c[i] = c[i-1] + c[i];
  }

  // 临时数组r，存储排序之后的结果
  int[] r = new int[n];
  // 计算排序的关键步骤，有点难理解
  for (int i = n - 1; i >= 0; --i) {
    int index = c[a[i]]-1;
    r[index] = a[i];
    c[a[i]]--;
  }

  // 将结果拷贝给a数组
  for (int i = 0; i < n; ++i) {
    a[i] = r[i];
  }
}
```



## Python

```
import itertools


class CountingSort:

    def __init__(self, array):
        self._array = array
        self._length = len(self._array)

    def sort(self):
        if self._length <= 1:
            return

        # counting list
        counting_list = [0] * (max(self._array)+1)

        for number in self._array:
            counting_list[number] += 1
        counting_list = list(itertools.accumulate(counting_list))

        set_tmp_array = [0]*self._length

        for number in reversed(self._array):
            index = counting_list[number] - 1
            set_tmp_array[index] = number
            counting_list[number] -= 1

        self._array[:] = set_tmp_array

        return self._array


if __name__ == '__main__':
    my_array = CountingSort([2, 5, 3, 0, 2, 3, 0, 3])
    print(my_array.sort())

```

> ```
> >>>
> [0, 0, 2, 2, 3, 3, 3, 5]
> ```
>
> 



# 基数排序 Radix sort

假设我们有10万个手机号码，希望将这10万个手机号码从小到大排序，刚刚这个问题里有这样的规律

假设要比较两个手机号码a，b的大小，如果在前面几位中，a手机号码已经比b手机号码大了，那后面的几位就不用看了。

借助稳定排序算法，这里有一个巧妙的实现思路。先按照最后一位来排序手机号码，然后，再按照倒数第二位重新排序，以此类推，最后按照第一位重新排序。经过11次排序之后，手机号码就都有序了。

注意，这里按照每位来排序的排序算法要是稳定的，否则这个实现思路就是不正确的。因为如果是非稳定排序算法，那最后一次排序只会考虑最高位的大小顺 序，完全不管其他位的大小关系，那么低位的排序就完全没有意义了。

根据每一位来排序，我们可以用刚讲过的桶排序或者计数排序，它们的时间复杂度可以做到O(n)。如果要排序的数据有k位，那我们就需要k次桶排序或者计数排序，总的时间复杂度是O(k*n)。当k不大的时候，比如手机号码排序的例子，k最大就是11，所以基数排序的时间复杂度就近似于O(n)。





这里有数组`[121, 432, 564, 23, 1, 45, 788]` , 最大值是788， 长度为3，需要循环三次，从后往前，依据单个位数进行排序， 

每一列采用计数排序

![image-20210323215604065](sorting.assets/image-20210323215604065.png)





## 特点

实际上，有时候要排序的数据并不都是等长的，比如我们排序牛津字典中的万个英文单词，最短的只有2个字母，最长的有44个字母，pneumonoultramicyoscpicailicovolcanoconiosis， 出现在韦氏辞典第八版的版本中意思是“吸入硅酸盐细末或石英岩灰而形成的肺尘埃沉着病，中文翻译是尘肺病。对于这种不等长的数据，基数排序还适用吗?

实际上，我们可以把所有的单词补齐到相同长度，位数不够的可以在后面补**“0”**，因为根据ASCII值，所有字母都大于“0”，所以补“0”不会影响到原有的大小顺序。这样就可以继续用基数排序了。

基数排序对要排序的数据是有要求的，需要可以分割出独立的**“**位**”**来比较，而且位之间有递进的关系，如果**a**数据的高位比**b**数据大，那剩下的低位就不用比较了。除此之外，每一位的数据范围不能太大，要可以用线性排序算法来排序，否则，基数排序的时间复杂度就无法做到**O(n)**了。







基数排序要求数据可以划分成高低位，位之间有递进关系。比较两个数，我们只需要比较高位，高位相同的再比较低位。而且每一位的数据范围不能太大，因为基数排序算法需要借助桶排序或者计数排序来完成 每一个位的排序工作。



是稳定排序算法，但不是原地排序算法



## Java

```
public class RadixSort {

    /**
     * 基数排序
     *
     * @param arr
     */
    public static void radixSort(int[] arr) {
        int max = arr[0];
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > max) {
                max = arr[i];
            }
        }

        // 从个位开始，对数组arr按"指数"进行排序
        for (int exp = 1; max / exp > 0; exp *= 10) {
            countingSort(arr, exp);
        }
    }

    /**
     * 计数排序-对数组按照"某个位数"进行排序
     *
     * @param arr
     * @param exp 指数
     */
    public static void countingSort(int[] arr, int exp) {
        if (arr.length <= 1) {
            return;
        }

        // 计算每个元素的个数
        int[] c = new int[10];
        for (int i = 0; i < arr.length; i++) {
            c[(arr[i] / exp) % 10]++;
        }

        // 计算排序后的位置
        for (int i = 1; i < c.length; i++) {
            c[i] += c[i - 1];
        }

        // 临时数组r，存储排序之后的结果
        int[] r = new int[arr.length];
        for (int i = arr.length - 1; i >= 0; i--) {
            r[c[(arr[i] / exp) % 10] - 1] = arr[i];
            c[(arr[i] / exp) % 10]--;
        }

        for (int i = 0; i < arr.length; i++) {
            arr[i] = r[i];
        }
    }
}
```





## Python

```
import itertools


class RadixSort:

    def __init__(self, array):
        self._array = array
        self._length = len(self._array)

    def sort(self):
        if self._length <= 1:
            return

        loop_times = len(str(max(self._array)))

        for loop_time in range(loop_times):
            bucket_list = [[] for i in range(10)]  # numbers are from 0 to 9, need 10
            for i in self._array:
                bucket_list[i // (10**loop_time) % 10].append(i)

            self._array = [j for i in bucket_list for j in i]
        return self._array


if __name__ == '__main__':
    my_array = RadixSort([121, 432, 564, 23, 1, 45, 788])
    print(my_array.sort())

```

> ```
> >>>
> [1, 23, 45, 121, 432, 564, 788]
> ```
>
> 







# 常用库排序分析



## qsort ( Glibc )

qsort() 会优先使用归并排序来排序输入数据，因为归并排序的空间复杂度是 O(n)，所以对于小数据量的排序，比如 1KB、2KB 等，归并排序额外需要 1KB、2KB 的内存空间，这个问题不大。现在计算机的内存都挺大的，我们很多时候追求的是速度。还记得我们前面讲过的用空间换时间的技巧吗？这就是一个典型的应用。

但如果数据量太大，就跟我们前面提到的，排序 100MB 的数据，这个时候我们再用归并排序就不合适了。所以，要排序的数据量比较大的时候，qsort() 会改为用快速排序算法来排序。那 qsort() 是如何选择快速排序算法的分区点的呢？如果去看源码，你就会发现，qsort() 选择分区点的方法就是“三数取中法”。

还有我们前面提到的递归太深会导致堆栈溢出的问题，qsort() 是通过自己实现一个堆上的栈，手动模拟递归来解决的。



实际上，qsort() 并不仅仅用到了归并排序和快速排序，它还用到了插入排序。在快速排序的过程中，当要排序的区间中，元素的个数小于等于 4 时，qsort() 就退化为插入排序，不再继续用递归来做快速排序，因为我们前面也讲过，在小规模数据面前，O(n2) 时间复杂度的算法并不一定比 O(nlogn) 的算法执行时间长

因为算法的性能可以通过时间复杂度来分析，但是，这种复杂度分析是比较偏理论的，如果我们深究的话，实际上时间复杂度并不等于代码实际的运行时间。

时间复杂度代表的是一个增长趋势，如果画成增长曲线图，你会发现 O(n2) 比 O(nlogn) 要陡峭，也就是说增长趋势要更猛一些。但是，我们前面讲过，在大 O 复杂度表示法中，我们会省略低阶、系数和常数，也就是说，O(nlogn) 在没有省略低阶、系数、常数之前可能是 O(knlogn + c)，而且 k 和 c 有可能还是一个比较大的数。

所以，对于小规模数据的排序，O(n^2) 的排序算法并不一定比 O(nlogn) 排序算法执行的时间长。对于小数据量的排序，我们选择比较简单、不需要递归的插入排序算法。

还记得我们之前讲到的哨兵来简化代码，提高执行效率吗？在 qsort() 插入排序的算法实现中，也利用了这种编程技巧。虽然哨兵可能只是少做一次判断，但是毕竟排序函数是非常常用、非常基础的函数，性能的优化要做到极致。



## java1.8

java1.8中的排序，在元素小于47的时候用插入排序，大于47小于286用双轴快排，大于286用timsort归并排序，并在timesort中记录数据的连续的有序段的的位置，若有序段太多，也就是说数据近乎乱序，则用双轴快排，当然快排的递归调用的过程中，若排序的子数组数据数量小，用插入排序。



针对 Java 语言：

1. 对于基本类型的数组，Java 采用的是双枢轴快速排序（Dual-Pivot Quicksort），这个算法是 Java 7 引入的。在此之前，Java 采用的是普通的快速排序，双枢轴快速排序是对普通快速排序的优化，新算法的实现代码位于类 java.util.DualPivotQuicksort 中。

2. 对于对象类型，Java 采用的算法是 TimSort，TimSort 算法也是 Java 7 引入的。在此之前，Java 采用的是归并排序。TimSort 算法实际上是对归并排序的一系列优化，TimSort 的实现代码位于类 java.util.TimSort 中。

3. 在这些排序算法中，如果数组长度比较小，它们还会采用效率更高的插入排序。



## golang

golang标准库中的Sort用的是快排+希尔排序+插排，数据量大于12时用快排，小于等于12时用6作为gap做一次希尔排序，然后走一遍普通的插排（插排对有序度高的序列效率高）。其中快排pivot的选择做了很多工作不是一两句话可以描述出来，是基于首中尾中值的很复杂的变种



## Array排序 (.NET)

1. 三个以内的，直接比较，交换进行实现
2. 大于3个小于16个的，用的是插入排序进行的实现
3. 对于大于16，并且深度限制是0的，用的是堆排序实现的
4. 对于大于15，并且深度限制不是0的，使用的是快速排序；然后快速排序分区使用的也是三数取中法





## javascript

Google v8中对QuickSort的实现是:
数据规模在10以内的话使用快排;
数据规模在10到1000之间时选择中点作为pivot进行快排;
数据规模在1000以上时，每隔200到215个数选一个数，将选出来的数排序，选择中间值作为pivot进行快排；
而且还有几个细节：
1是折半的时候用的是位运算；
2是每一次遍历都会分成小于pivot，等于pivot，大于pivot的三个区间；
3是小于pivot和大于pivot这两个区间中数据规模比较小的会递归执行QuickSort，数据规模大的会先通过while循环减小数据规模。
附上源码链接: https://github.com/v8/v8/blob/master/src/js/array.js



# Appendix

https://time.geekbang.org/column/article/41802

https://www.hackerearth.com/practice/algorithms/sorting/bubble-sort/tutorial/

https://medium.com/@paulsoham/merge-sort-63d75df76388

https://stackabuse.com/quicksort-in-javascript/

https://www.programiz.com/dsa/bucket-sort

https://www.programiz.com/dsa/counting-sort

https://www.techgeekbuzz.com/radix-sort

https://zhuanlan.zhihu.com/p/143127754

https://time.geekbang.org/column/article/42359

