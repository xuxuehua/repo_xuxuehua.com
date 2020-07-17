---
title: "nose"
date: 2020-06-16 11:46
---
[toc]



# nose

nose可以自动识别继承于unittest.TestCase的测试单元，并执行测试，而且，nose也可以测试非继承于unittest.TestCase的测试单元。nose提供了丰富的API便于编写测试代码。

只要遵循一些简单的规则去组织你的类库和测试代码，nose是可以自动识别单元测试的。执行测试是非常耗资源的，但是，一段第一个测试模块被加载后，nose就开始执行测试。

nose 是一个比 unittest 更加先进的测试框架



## 特点

不需要编写继承unittest.TestCase方法的测试类。你甚至可以将测试作为独立的函数来编写

自动查找和搜集测试，不需要自己手动搭建测试集；

支持插件，可以搭配其他非常实用的标准化插件（coverage, output capture, drop into debugger on errors, doctests support, profiler）

为测试打标签，并且可以根据标签非常灵活的选择测试集；

并行测试；

更好的支持fixtures；

产生器测试。



包的测试收集按照树的层级级别一级一级进行，因此package.tests、package.sub.tests、package.sub.sub2.tests将会被收集。

匹配的正则表达式默认值为：

```
(`(?:^|[\\b_\\.-])[Tt]est.
```

所以最好是以Test开头，或者test开头。当然也可以修改默认的匹配的正则表达式。

匹配成功的包、任何python的源文件都会当做测试用例。



## installation

```
pip install nose
```





# nosetests

```
 nosetests [options] [(optional) test files or directories]
```





## 选择测试用例

将需要测试的名称传递给nose的命令行。格式如下：

```
nosetests only_test_this.py
```

测试的名称可以是脚本文件的名称或者模块的名称，也可以使用colon表达式表达的测试名称。路径可以是相对的路径也可以是绝对的路径。如下所示：

```
nosetests test.module
nosetests another.test:TestCase.test_method
nosetests a.test:TestCase
nosetests /path/to/test/file.py:test_function
```

同样可以使用-w开关来切换当前的工作路径，从而改变nose查找测试用例的根路径。

```
nosetests -w /path/to/tests
```





## 配置

除了使用命令行这种方式之外，还可以在根目录下放置配置文件，配置文件的类型为.noserc或nose.cfg文件。配置文件都是标准的ini内容格式

```
[nosetests]
verbosity=3
with-doctest=1
```



# example



## 测试代码

```
from nose.tools import eq_
from nose.tools import assert_equal


class NoseTest:
    a = 1
    b = 2
    # assert_equal(a, b, '%a != %a'%(a,b))
    eq_(a, b)

>>>
Failure: AssertionError (1 != 2) ... FAIL

======================================================================
FAIL: Failure: AssertionError (1 != 2)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/site-packages/nose/failure.py", line 39, in runTest
    raise self.exc_val.with_traceback(self.tb)
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/site-packages/nose/loader.py", line 418, in loadTestsFromName
    addr.filename, addr.module)
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/site-packages/nose/importer.py", line 47, in importFromPath
    return self.importFromDir(dir_path, fqname)
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/site-packages/nose/importer.py", line 94, in importFromDir
    mod = load_module(part_fqname, fh, filename, desc)
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/imp.py", line 234, in load_module
    return load_source(name, filename, file)
  File "/Users/rxu/.local/share/virtualenvs/test_only-bQB_6HEy/lib/python3.7/imp.py", line 171, in load_source
    module = _load(spec)
  File "<frozen importlib._bootstrap>", line 696, in _load
  File "<frozen importlib._bootstrap>", line 677, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 728, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/Users/rxu/coding/python/python3/test_only/my_nose.py", line 5, in <module>
    class NoseTest:
  File "/Users/rxu/coding/python/python3/test_only/my_nose.py", line 9, in NoseTest
    eq_(a, b)
AssertionError: 1 != 2

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
```

