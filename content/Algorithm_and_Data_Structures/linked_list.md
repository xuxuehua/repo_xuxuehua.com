---
title: "Linked_list"
date: 2019-12-22 13:58

---

[TOC]

# 链表

![Array vs Linked List](linked_list.assets/array-vs-linked-list.png)

链表通过指针将一组零散的内存块串联在一起。其中，我们把内存块称为链表的“结点”

为了将所有的结点串起来，每个链表的结点除了存储数据之外，还需要记录链上的下一个结点的地址。这个记录下个结点地址的指针叫作**后继指针next**



## 特点

与数组一样，链表也支持数据的查找、插入和删除操作

在进行数组的插入、删除操作时，为了保持内存数据的连续性，需要做大量的数据搬移，所以时间复杂度是O(n)

而在链表中插入或者删除一个数据，我们并不需要为了保持内存的连续性而搬移结点，因为链表的存储空间本身就不是连续的。所以，在链表中插入和删除一个数据是非常快速的。

链表中的节点在内存中不是连续分布的 ，而是散乱分布在内存中的某地址上，分配机制取决于操作系统的内存管理。

但是，有利就有弊。链表要想随机访问第k个元素，就没有数组那么高效了。因为链表中的数据并非连续存储的，所以无法像数组那样，根据首地址和下标，通过寻址公式就能直接计算出对应的内存地址，而是需要根据指针一个结点一个结点地依次遍历，直到找到相应的结点





## 单链表 singly Linked List （常用）

![image-20210209110557064](linked_list.assets/image-20210209110557064.png)

链表的入口点称为头节点head

两个结点是比较特殊的，它们分别是第一个结点和最后一个结点。习惯性地把第一个结点叫作头结点，把最后一个结点叫作尾结点。

其中，头结点用来记录链表的基地址。有了它，我们就可以遍历得到整条链表。

而尾结点特殊的地方是:指针不是指向下一个结点，而是指向一个空地址NULL，表示这是链表上最后一个结点。



```
from typing import Optional


class Node:
    def __init__(self, data: int, next_point_address=None):
        self.data = data
        self.next_point_address = next_point_address


class SinglyLinkedList:
    def __init__(self):
        self.head_point_address = None

    def find_by_value(self, value: int) -> Optional[Node]:
        current_point_address = self.head_point_address
        while current_point_address and current_point_address.data != value:
            current_point_address = current_point_address.next_point_address
        return current_point_address

    def find_by_index(self, index: int) -> Optional[Node]:
        current_point_address = self.head_point_address
        position = 0
        while current_point_address and position != index:
            current_point_address = current_point_address.next_point_address
            position += 1
        return current_point_address

    def insert_value_to_head(self, value: int):
        new_node = Node(value)
        self.insert_node_to_head(new_node)

    def insert_node_to_head(self, new_node: Node):
        new_node.next_point_address = self.head_point_address
        self.head_point_address = new_node

    def insert_value_after_node(self, node: Node, value: int):
        new_node = Node(value)
        self.insert_node_after_node(node, new_node)

    def insert_node_after_node(self, node: Node, new_node: Node):
        if not node or not new_node:
            return
        new_node.next_point_address = node.next_point_address
        node.next_point_address = new_node

    def insert_value_before_node(self, node: Node, value: int):
        new_node = Node(value)
        self.insert_node_before_node(node, new_node)

    def insert_node_before_node(self, node: Node, new_node: Node):
        if not self.head_point_address or not node or not new_node:
            return
        if self.head_point_address == node:
            self.insert_node_to_head(new_node)
            return

        current_point_address = self.head_point_address
        while current_point_address.next_point_address and current_point_address.next_point_address != node:
            current_point_address = current_point_address.next_point_address

        if not current_point_address.next_point_address:
            return

        new_node.next_point_address = node
        current_point_address.next_point_address = new_node

    def delete_by_node(self, node: Node):
        if not self.head_point_address or not node:
            return
        if node == self.head_point_address:
            self.head_point_address = node.next_point_address
            return

        current_point_address = self.head_point_address
        while current_point_address and current_point_address.next_point_address != node:
            current_point_address = current_point_address.next_point_address
        if not current_point_address:
            return
        current_point_address.next_point_address = node.next_point_address

    def delete_by_value(self, value: int):
        if not self.head_point_address or not value:
            return

        tmp_node = Node(None)
        tmp_node.next_point_address = self.head_point_address
        tmp_node_point_address = tmp_node
        current_point_address = self.head_point_address
        while current_point_address:
            if current_point_address.data != value:
                tmp_node_point_address.next_point_address = current_point_address
                tmp_node_point_address = tmp_node_point_address.next_point_address
            current_point_address = current_point_address.next_point_address
        if tmp_node_point_address.next_point_address:
            tmp_node_point_address.next_point_address = None
        self.head_point_address = tmp_node.next_point_address

    @staticmethod
    def reverse(node: Node):
        new_l = SinglyLinkedList()
        current_point_address, previous_point_address = node, None
        while current_point_address:
            previous_point_address, previous_point_address.next_point_address, current_point_address = current_point_address, previous_point_address, current_point_address.next_point_address
            new_l.insert_node_to_head(previous_point_address)
        return new_l

    def __repr__(self) -> str:
        nums = []
        current_point_address = self.head_point_address
        while current_point_address:
            nums.append(current_point_address.data)
            current_point_address = current_point_address.next_point_address
        return "->".join(str(num) for num in nums)

    def __iter__(self):
        node = self.head_point_address
        while node:
            yield node.data
            node = node.next_point_address

    def print_all(self):
        current_point_address = self.head_point_address
        if current_point_address:
            print(f"current_point_address={current_point_address}", end="")
            current_point_address = current_point_address.next_point_address

        while current_point_address:
            print(f"->current_point_address.data={current_point_address.data}", end="")
            current_point_address = current_point_address.next_point_address
        print("\n", flush=True)


if __name__ == '__main__':
    test_l = SinglyLinkedList()
    for i in range(10):
        test_l.insert_value_to_head(i)
    print(test_l)

    node5 = test_l.find_by_value(5)
    print(node5)

    node4 = test_l.find_by_index(4)
    print(node4)

    node11 = test_l.insert_value_before_node(node=node4, value=11)
    print(test_l)

    node1 = test_l.find_by_value(1)
    node12 = test_l.insert_value_after_node(node=node1, value=12)
    print(test_l)

    node0 = test_l.find_by_value(0)
    test_l.delete_by_node(node0)
    print(test_l)

    test_l.delete_by_value(11)
    print(test_l)

    node = test_l.find_by_value(9)
    print(SinglyLinkedList.reverse(node))
```

> ```
> >>>
> 9->8->7->6->5->4->3->2->1->0
> <__main__.Node object at 0x103579100>
> <__main__.Node object at 0x103579100>
> 9->8->7->6->11->5->4->3->2->1->0
> 9->8->7->6->11->5->4->3->2->1->12->0
> 9->8->7->6->11->5->4->3->2->1->12
> 9->8->7->6->5->4->3->2->1->12
> 12->1->2->3->4->5->6->7->8->9
> ```









## 双向链表 Doubly Linked List （实际开发常用）

![image-20210209110733422](linked_list.assets/image-20210209110733422.png)

每一个节点有两个指针域，一个指向下一个节点，一个指向上一个节点。

如果存储同样多的数据，双向链表要比单链表占用更多的内存空间。虽然两个指针比较浪费存储空间，但可以支持双向遍历，这样也带来了双向链表操作的灵活性。

从结构上来看，双向链表可以支持O(1)时间复杂度的情况下找到前驱结点，正是这样的特点，也使双向链表在某些情况下的插入、删除等操作都要比单链表简单、高效。



### 删除操作

在实际的软件开发中，从链表中删除一个数据无外乎这两种情况: 

* 删除结点中**值等于某个给定值**的结点

不管是单链表还是双向链表，为了查找到值等于给定值的结点，都需要从头结点开始一个一个依次遍历对比，直到找到值等于给定值的结点，然后再通过我前面讲的指针操作将其删除。尽管单纯的删除操作时间复杂度是O(1)，但遍历查找的时间是主要的耗时点，对应的时间复杂度为O(n)。根据时间复杂度分析中的加法法则，删除值等于给定值的结点对应的链表操作的总时间复杂度为O(n)。



* 删除给定指针指向的结点

已经找到了要删除的结点，但是删除某个结点q需要知道其前驱结点，而单链表并不支持直接获取前驱结点，所以，为了找到前驱结点，还是要从头结点开始遍历链表，直到p->next=q，说明p是q的前驱结点。

但是对于双向链表来说，这种情况就比较有优势了。因为双向链表中的结点已经保存了前驱结点的指针，不需要像单链表那样遍历。所以，针对第二种情况，单链表删除操作需要O(n)的时间复杂度，而双向链表只需要在O(1)的时间复杂度内就搞定了!



### 优势

如果希望在链表的某个指定结点前面插入一个结点，双向链表比单链表有很大的优势。双向链表可以在O(1)时间复杂度搞定，而单向链表需要O(n)的时间复杂度。

除了插入、删除操作有优势之外，对于一个有序链表，双向链表的按值查询的效率也要比单链表高一些。因为，我们可以记录上次查找的位置p，每次查询时，根据要查找的值与p的大小关系，决定是往前还是往后查找，所以平均只需要查找一半的数据。



### LinkedHashMap (Java)

在实际的软件开发中，双向链表尽管比较费内存，但还是比单链表的应用更加广泛的原因。
如果你熟悉Java语言，你肯定用过LinkedHashMap这个容器。如果你深入研究LinkedHashMap的实现原理，就会发现其中就用到了双向链表这种数据结构。



### 缓存

缓存实际上就是利用了空间换时间的设计思想。如果我们把数据存储在硬盘上，会比较节省内存，但每次查找数据都要询问一次硬盘，会
比较慢。但如果我们通过缓存技术，事先将数据加载在内存中，虽然会比较耗费内存空间，但是每次数据查询的速度就大大提高了。



## 循环链表 Cyclic Linked List

![image-20210209111758272](linked_list.assets/image-20210209111758272.png)

和单链表相比，循环链表的优点是从链尾到链头比较方便。当要处理的数据具有环型结构特点时，就特别适合采用循环链表。

比如著名的约瑟夫问题。尽管用单链表也可以实现，但是用循环链表实现的话，代码就会简洁很多



## 双向循环链表 Doubly Linked List

![image-20210209112223992](linked_list.assets/image-20210209112223992.png)

# 链表VS数组

| 时间复杂度 | 数组   | 链表   |
| ----- | ---- | ---- |
| 插入删除  | O(n) | O(1) |
| 随机访问  | O(1) | O(n) |

数组和链表的对比，并不能局限于时间复杂度。而且，在实际的软件开发中，不能仅仅利用复杂度分析就决定使用哪个数据结构来存储数据。



## CPU 缓存

数组简单易用，在实现上使用的是连续的内存空间，可以借助CPU的缓存机制，预读数组中的数据，所以访问效率更高。而链表在内存中并不是连续存储，所以数组的缺点是大小固定，一经声明就要占用整块连续内存空间。如果声明的数组过大，系统可能没有足够的连续内存空间分配给它，导致“内存不足(out of memory)”。如果声明的数组过小，则可能出现不够用的情况。这时只能再申请一个更大的内存空间，把原数组拷贝进去，非常费时。链表本身没有大小的限制，天然地支持动态扩容，我觉得这也是它与数组最大的区别。

我们Java中的ArrayList容器，也可以支持动态扩容啊?当往支持动态扩容的数组中插入一个数据时，如果数组中没有空闲空间了，就会申请一个更大的空间，将数据拷贝过去，而数据拷贝的操作是非常耗时的。

如果用ArrayList存储了了1GB大小的数据，这个时候已经没有空闲空间了，当我们再插入数据的时候，ArrayList会申请一
个1.5GB大小的存储空间，并且把原来那1GB的数据拷贝到新申请的空间上。听起来是不是就很耗时?

链表对CPU缓存不友好，没办法有效预读。除此之外，如果你的代码对内存的使用非常苛刻，那数组就更适合你。因为链表中的每个结点都需要消耗额外的存储空间去存储一份指向下一个结点的指针，所以内存消耗会翻倍。而且，对链表进行频繁的插入、删除操作，还会导致频繁的内存申请和释放，容易造成内存碎片，如果是Java语言，就有可能会导致频繁的GC(Garbage Collection，垃圾回收)。 所以，在我们实际的开发中，针对不同类型的项目，要根据具体情况，权衡究竟是选择数组还是链表。



# 缓存

缓存是一种提高数据读取性能的技术，在硬件设计、软件开发中都有着非常广泛的应用，比如常见的CPU缓存、数据库缓存、浏览器缓存等等。

缓存的大小有限，当缓存被用满时，哪些数据应该被清理出去，哪些数据应该被保留?这就需要缓存淘汰策略来决定。

常见的策略有三种:

* 先进先出策略FIFO(First In，First Out)

* 最少使用策略LFU(Least Frequently Used)

* 最近最少使用策略LRU(Least Recently Used)



## LRU  实现

我们维护一个有序单链表，越靠近链表尾部的结点是越早之前访问的。当有一个新的数据被访问时，我们从链表头开始顺序遍历链表。 

1.如果此数据之前已经被缓存在链表中了，我们遍历得到这个数据对应的结点，并将其从原来的位置删除，然后再插入到链表的头部。 

2.如果此数据没有在缓存链表中，又可以分为两种情况:

* 如果此时缓存未满，则将此结点直接插入到链表的头部
* 如果此时缓存已满，则链表尾结点删除，将新的数据结点插入链表的头部





## CPU缓存处理

CPU在从内存读取数据的时候，会先把读取到的数据加载到CPU的缓存中。而CPU每次从内存读取数据并不是只读取那个特定要访问的地址，而是读取一个 数据块并保存到CPU缓存中，然后下次访问内存数据的时候就会先从CPU缓存开始查找，如果找到就不需要再从内存中取。

这样就实现了比内存访问速度更快的机制，也就是CPU缓存存在的意义:为了弥补内存访问速度过慢与CPU执行速度快之间的差异而引入。







# 引用和指针

有些语言有“指针”的概念，比如C语言

有些语言没有指针，取而代之的是“引用”，比如Java、Python。

不管是“指针”还是“引用”，实际上，它们的意思都是一样的，都是存储所指对象的内存地址



## 指针核心

将某个变量赋值给指针，实际上就是将这个变量的地址赋值给指针，或者反过来说，指针中存储了这个变量的内存地址，指向了这个变量，通过指针就能找到这个变量。



## 含义

在编写链表代码的时候，我们经常会有这样的代码

```
p->next=q
```

>  p结点中的next指针存储了q结点的内存地址

还有一个更复杂的，也是我们写链表代码经常会用到的

```
p->next=p->next->next
```

> p结点的next指针存储了p结点的下下一个结点的内存地址



## 小心丢失指针

![image-20210213115551182](linked_list.assets/image-20210213115551182.png)

若希望在结点a和相邻的结点b之间插入结点x，假设当前指针p指向结点a。如果我们将代码实现变成下面这个样子，就会发生指针丢失和内存泄露。

```
p->next = x; // 将p的next指针指向x结点;
x->next = p->next; // 将x的结点的next指针指向b结点;
```

> 指针在完成第一步操作之后，已经不再指向结点b了，而是指向结点x
>
> 第2行代码相当于将x赋值给x->next，自己指向自己。因此，整个链表也就断成了两半，从结点b往后的所有结点都无法访问到了。





对于有些语言来说，比如C语言，内存管理是由程序员负责的，如果没有手动释放结点对应的内存空间，就会产生内存泄露。所以，我们插入结点时，一定要注意操作的顺序，要先将结点x的next指针指向结点b，再把结点a的next指针指向结点x，这样才不会丢失指针，导致内存泄漏。所以，对于刚刚的插入代码，我们只需要把第1行和第2行代码的顺序颠倒一下就可以了。

```
x->next = p->next;
p->next = x;
```

同理，删除链表结点时，也一定要记得手动释放内存空间，否则，也会出现内存泄漏的问题。当然，对于像Java这种虚拟机自动管理内存的编程语言来说，就不需要考虑这么多了。







## 插入操作

```
new_node->next = p->next;
p->next = new_node;
```



若我们要向一个空链表中插入第一个结点，刚刚的逻辑就不能用了。我们需要进行下面这样的特殊处理，其中head表示链表的头结点。所以，从这段代码，我们可以发现，对于单链表的插入操作，第一个结点和其他结点的插入逻辑是不一样的。

```
if (head == null) { 
		head = new_node;
}
```

> head=null表示链表中没有结点了





## 删除操作

![image-20210213115814439](linked_list.assets/image-20210213115814439.png)

如果要删除结点p的后继结点，我们只需要一行代码就可以搞定。

```
p->next = p->next->next;
```



如果我们要删除链表中的最后一个结点，前面的删除代码就不work了。跟插入类似，我们也需要对于这种情况特殊处理。

```
if (head->next == null) { 
		head = null;
}
```



## 哨兵节点 (解决边界问题，推荐)

针对链表的插入、删除操作，需要对插入第一个结点和删除最后一个结点的情况进行特殊处理。这样代码实现起来就会
很繁琐，不简洁，而且也容易因为考虑不全而出错。



引入哨兵结点，在任何时候，不管链表是不是空，head指针都会一直指向这个哨兵结点。我们也把这种有哨兵结点的链表叫带头链表。相反，没有哨兵结点的链表就叫作不带头链表。

![image-20210214120204040](linked_list.assets/image-20210214120204040.png)



利用哨兵简化编程难度的技巧，在很多代码实现中都有用到，比如插入排序、归并排序、动态规划等

### 代码对比

代码1 

```
// 在数组a中，查找key，返回key所在的位置 
// 其中，n表示数组a的长度

int find(char*a, int n, char key) {
		// 边界条件处理，如果a为空，或者n<=0，说明数组中没有数据，就不用while循环比较了
		if (a == null || n <= 0) {
				return -1;
		}
		int i = 0;
		// 这里有两个比较操作:i<n和a[i]==key.
		while (i < n) {
				if (a[i] == key) {
						return i;
				}
				++i;
		}
		return -1;
}
```



代码2

```
// 在数组a中，查找key，返回key所在的位置 
// 其中，n表示数组a的长度
// a = {4, 2, 3, 5, 9, 6} n=6 key = 7
// a = {4, 2, 3, 5, 9, 6} n=6 key = 6

int find(char* a, int n, char key) {
		if (a == null || n <= 0) {
				return -1;
		}
		// 这里因为要将a[n-1]的值替换成key，所以要特殊处理这个值
		if (a[n-1] == key) {
				return n-1;
		}
		// 把a[n-1]的值临时保存在变量tmp中，以便之后恢复。tmp=6。
		// 之所以这样做的目的是:希望find()代码不要改变a数组中的内容
		char tmp = a[n-1];
		// 把key的值放到a[n-1]中，此时a = {4, 2, 3, 5, 9, 7}
		a[n-1] = key;
		int i = 0;
		// while 循环比起代码一，少了i<n这个比较操作
		while (a[i] != key) {
				++i;
		}
		// 恢复a[n-1]原来的值,此时a= {4, 2, 3, 5, 9, 6}
		a[n-1] = tmp;
		if (i == n-1) {
				// 如果i == n-1说明，在0...n-2之间都没有key，所以返回-1
				return -1;
		} else {
				// 否则，返回i，就是等于key值的元素的下标
				return i;
		}
}
```



对比两段代码，在字符串a很长的时候，比如几万、几十万，你觉得哪段代码运行得更快点呢?

答案是代码二，因为两段代码中执行次数最多就是while循环那一部 分。第二段代码中，我们通过一个哨兵a[n-1] = key，成功省掉了一个比较语句i<n，不要小看这一条语句，当累积执行万次、几十万次时，累积的时间就很明显 了。

当然，这只是为了举例说明哨兵的作用，你写代码的时候千万不要写第二段那样的代码，因为可读性太差了。大部分情况下，我们并不需要如此追求极致的性能。



## 边界检查

软件开发中，代码在一些边界或者异常情况下，最容易产生Bug。链表代码也不例外。要实现没有Bug的链表代码，一定要在编写的过程中以及编写完成之后，检查边界条件是否考虑全面，以及代码在边界条件下是否能正确运行



我经常用来检查链表代码是否正确的边界条件有这样几个

* 如果链表为空时，代码是否能正常工作?
* 如果链表只包含一个结点时，代码是否能正常工作?
* 如果链表只包含两个结点时，代码是否能正常工作?
* 代码逻辑在处理头结点和尾结点的时候，是否能正常工作?



实际上，不光光是写链表代码，你在写任何代码时，也千万不要只是实现业务正常情况下的功能就好了，一定要多想想，你的代码在运行的时候，可能会遇到哪些边界情况或者异常情况。遇到了应该如何应对，这样写出来的代码才够健壮!



## 举例法和画图法

对于稍微复杂的链表操作，比如前面我们提到的单链表反转，指针一会儿指这，一会儿指那，一会儿就被绕晕了。总感觉脑容量不够，想不清楚。所以这个时候就要使用举例法和画图法



![image-20210213123652815](linked_list.assets/image-20210213123652815.png)

看图写代码，是不是就简单多啦?而且，当我们写完代码之后，也可以举几个例子，画在纸上，照着代码走一遍，很容易就能发现代码中的Bug。



# 常见的链表操作

## 单链表反转



## 链表中环的检测 



## 两个有序的链表合并 



## 删除链表倒数第n个结点 



## 求链表的中间结点



