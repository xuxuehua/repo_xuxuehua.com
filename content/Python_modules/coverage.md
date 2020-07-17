---
title: "coverage"
date: 2020-07-16 08:28
---
[toc]



# Coverage



## installation

```
pip install coverage
```



## Helloworld

do not include `.py` suffix 

```
$ coverage run -m my_unit_test2 &&  coverage report
Inside suite()
This is test_a1
.This is test_a2
.This is test_b1
.This is test_b2
.This method will not be called by default
.
----------------------------------------------------------------------
Ran 5 tests in 0.000s

OK
Name               Stmts   Miss  Cover
--------------------------------------
my_unit_test2.py      27      1    96%
```

