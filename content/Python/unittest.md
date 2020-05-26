---
title: "unittest"
date: 2019-09-24 08:43
---
[TOC]



# 单元测试

单元测试，通俗易懂地讲，就是编写测试来验证某一个模块的功能正确性，一般会指定输入，验证输出是否
符合预期







## Hello World

```
import unittest
# 将要被测试的排序函数 def sort(arr):
      l = len(arr)
      for i in range(0, l):
          for j in range(i + 1, l):
              if arr[i] >= arr[j]:
                  tmp = arr[i]
                  arr[i] = arr[j]
                  arr[j] = tmp
# 编写子类继承unittest.TestCase 
class TestSort(unittest.TestCase):
# 以test开头的函数将会被测试, 即只有test开头才会被测试
		def test_sort(self):
				arr = [3, 4, 1, 5, 6]
				sort(arr)
				# assert 结果跟我们期待的一样 
				self.assertEqual(arr, [1, 3, 4, 5, 6])

if __name__ == '__main__':
		## 如果在Jupyter下，请用如下方式运行单元测试 
		unittest.main(argv=['first-arg-is-ignored'], exit=False)
# 如果是命令行下运行，则: 
		## unittest.main()

>>>
Ran 2 tests in 0.002s
OK
```





## 分类及特点

小型测试，针对单个函数的测试，关注其内部逻辑，mock所有需要的服务。小型测试带来优秀的代码质量、良好的异常处理、优雅的错误报告

中型测试，验证两个或多个制定的模块应用之间的交互

大型测试，也被称为“系统测试”或“端到端测试”。大型测试在一个较高层次上运行，验证系统作为一个整体是如何工作的



## 集成测试

集成测试则要把好几个单元组装到一起才能测试，测试通过的前提条件是，所有这些单元都写好了，这个周期就明显比单元测试要长；系统测试则要把整个系统的各个模块都连在一起，各种数据都准备好，才可能通过。







# 内置方法





## mock 

mock是单元测试中最核心重要的一环。mock的意思，便是通过一个虚假对象，来代替被测试函数或模块需 要的对象。 

举个例子，比如你要测一个后端API逻辑的功能性，但一般后端API都依赖于数据库、文件系统、网络等。这 样，你就需要通过mock，来创建一些虚假的数据库层、文件系统层、网络层对象，以便可以简单地对核心 后端逻辑单元进行测试。



Python mock则主要使用mock或者MagicMock对象

```
import unittest

from unittest.mock import MagicMock


class A(unittest.TestCase):
    def m1(self):
        val = self.m2()
        self.m3(val)

    def m2(self):
        pass

    def m3(self, val):
        pass

    def test_m1(self):
        a = A()
        a.m2 = MagicMock(return_value='custom_val')
        a.m3 = MagicMock()
        a.m1()
        self.assertTrue(a.m2.called) #验证m2被call过
        a.m3.assert_called_with("custom_val") #验证m3被指定参数call过

if __name__ == "__main__":
    unittest.main(argv=['first-arg-is-ignored'], exit=False)
    
>>>
Ran 1 test in 0.001s

OK
```

> 我们需要对m1()进行单元测试，但是m1()取决于m2()和m3()。如果m2()和m3()的内部比较复杂, 你就不能只是简单地调用m1()函数来进行测试，可能需要解决很多依赖项的问题。
>
> 有了mock其实就很好办了。我们可以把m2()替换为一个返回具体数值的value，把m3()替换为另一个mock(空函数)。这样，测试m1()就很容易了，我们可以测试m1()调用m2()，并且用m2()的返回值调用m3()。



## Mock Side Effect

就是 mock的函数，属性是可以根据不同的输入，返回不同的数值，而不只是一个return_value



测试的是输入参数是否为负数，输入小于0则输出为1 ，否则输出为2

```
from unittest.mock import MagicMock

def side_effect(arg):
    if arg < 0:
        return 1
    else:
        return 2

mock = MagicMock()
mock.side_effect = side_effect

print(mock(-1))
print(mock(1))

>>>
1
2
```





## patch

给开发者提供了非常便利的函数mock方法。它可以应用Python的decoration模式或是context manager概念，快速自然地mock所需的函数

```
from unittest.mock import patch

@patch('sort')
def test_sort(self, mock_sort):
		...
```





另一种patch的常见用法，是mock类的成员函数，这个技巧我们在工作中也经常会用到，比如说一个类的构
造函数非常复杂，而测试其中一个成员函数并不依赖所有初始化的object

```
with patch.object(A, '__init__', lambda x: None):
		...
```

> 在with语句里面，我们通过patch，将A类的构造函数mock为一个do nothing的函
> 数，这样就可以很方便地避免一些复杂的初始化(initialization)





## 模块化

高质量单元测试，不仅要求我们提高Test Coverage，尽量让所写的测试能够cover每个模块中的每条语句;
还要求我们从测试的角度审视codebase，去思考怎么模块化代码，以便写出高质量的单元测试。



```
def work(arr):
      # pre process
      ...
      ...
      # sort
      l = len(arr)
      for i in range(0, l):
          for j in range(i + 1, j):
              if arr[i] >= arr[j]:
                  tmp = arr[i]
                  arr[i] = arr[j]
                  arr[j] = tmp
      # post process
      ...
      ...
      Return arr
```



正确的测试方法，应该是先模块化代码，写成下面的形式

```
def preprocess(arr):
      ...
      ...
      return arr
  def sort(arr):
      ...
      ...
      return arr
  def postprocess(arr):
      ...
return arr
  def work(self):
      arr = preprocess(arr)
      arr = sort(arr)
      arr = postprocess(arr)
      return arr
```



接着再进行相应的测试，测试三个子函数的功能正确性;然后通过mock子函数，调用work()函数，来验证
三个子函数被call过

```
from unittest.mock import patch
  def test_preprocess(self):
      ...
  def test_sort(self):
      ...
  def test_postprocess(self):
      ...
  @patch('%s.preprocess')
  @patch('%s.sort')
  @patch('%s.postprocess')
  def test_work(self,mock_post_process, mock_sort, mock_preprocess):
      work()
      self.assertTrue(mock_post_process.called)
      self.assertTrue(mock_sort.called)
      self.assertTrue(mock_preprocess.called)
```

