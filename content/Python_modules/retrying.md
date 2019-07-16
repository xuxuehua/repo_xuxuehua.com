---
title: "retrying"
date: 2019-07-12 14:44
---
[TOC]



# retrying



```
pip install retrying
```



```
class Retrying(object):

    def __init__(self,
                 stop=None, wait=None,
                 stop_max_attempt_number=None,
                 stop_max_delay=None,
                 wait_fixed=None,
                 wait_random_min=None, wait_random_max=None,
                 wait_incrementing_start=None, wait_incrementing_increment=None,
                 wait_exponential_multiplier=None, wait_exponential_max=None,
                 retry_on_exception=None,
                 retry_on_result=None,
                 wrap_exception=False,
                 stop_func=None,
                 wait_func=None,
                 wait_jitter_max=None):
```



## stop_max_attempt_number

用来设定最大的尝试次数，超过该次数就停止重试

最后一次如果还是有异常则会抛出异常，停止运行，默认为5次





## stop_max_delay

比如设置成10000，那么从被装饰的函数开始执行的时间点开始，到函数成功运行结束或者失败报错中止的时间点，只要这段时间超过10秒，函数就不会再执行了



```
from retrying import retry
from time import sleep

@retry(stop_max_attempt_number=3, stop_max_delay=3000)
def run():
    n = 0
    t = 1    
    for i in range(10):
        print('start run')
        n += 1
        t += 1
        print('n%s, t%s' % (n, t))
        sleep(t)
        raise NameError


if __name__ == "__main__":
    run()

>>>
start run
n1, t2
start run
n1, t2
Traceback (most recent call last):
  File "c2.py", line 18, in <module>
    run()
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 49, in wrapped_f
    return Retrying(*dargs, **dkw).call(f, *args, **kw)
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 212, in call
    raise attempt.get()
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 247, in get
    six.reraise(self.value[0], self.value[1], self.value[2])
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/six.py", line 693, in reraise
    raise value
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 200, in call
    attempt = Attempt(fn(*args, **kwargs), attempt_number, False)
  File "c2.py", line 14, in run
    raise NameError
NameError
```





## wait_fixed

设置在两次retrying之间的停留时间, 如果出现异常则会一直重复调用，默认 1000毫秒

```
from retrying import retry
from time import sleep

@retry(wait_fixed=2000)
def run():
    print('start run')
    raise NameError

if __name__ == "__main__":
    run()
    
>>>
start run
start run
start run
....
```







## wait_random_min/wait_random_max

用随机的方式产生两次retrying之间的停留时间

在两次调用方法停留时长，停留最短时间，默认为0





## wait_exponential_multiplier/wait_exponential_max

以指数的形式产生两次retrying之间的停留时间，产生的值为2^previous_attempt_number * wait_exponential_multiplier，previous_attempt_number是前面已经retry的次数，如果产生的这个值超过了wait_exponential_max的大小，那么之后两个retrying之间的停留值都为wait_exponential_max





## retry_on_exception

指定一个函数，如果此函数返回指定异常，则会重试，如果不是指定的异常则会退出

```
from retrying import retry
from time import sleep

def run2(exception):
	return isinstance(exception, ZeroDivisionError)

@retry(stop_max_attempt_number=3, retry_on_exception=run2)
def run():
	print('start run')
	a = 1 / 0
	sleep(3)

if __name__ == "__main__":
	run()

>>>
start run
start run
start run
Traceback (most recent call last):
  File "c2.py", line 14, in <module>
    run()
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 49, in wrapped_f
    return Retrying(*dargs, **dkw).call(f, *args, **kw)
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 212, in call
    raise attempt.get()
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 247, in get
    six.reraise(self.value[0], self.value[1], self.value[2])
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/six.py", line 693, in reraise
    raise value
  File "/Users/rxu/.pyenv/versions/env3.7.0/lib/python3.7/site-packages/retrying.py", line 200, in call
    attempt = Attempt(fn(*args, **kwargs), attempt_number, False)
  File "c2.py", line 10, in run
    a = 1 / 0
ZeroDivisionError: division by zero
```



## retry_on_result

指定一个函数，如果指定的函数返回True，则重试，否则抛出异常退出

```
from retrying import retry
from time import sleep

def run2(r):
	print('exec run2')
	return False

@retry(retry_on_result=run2)
def run():
	print('start run')
	a = 1 
	sleep(3)
	return a

if __name__ == "__main__":
	run()

>>>
start run
exec run2
```





## wrap_exception

参数设置为True/False，如果指定的异常类型，包裹在RetryError中，会看到RetryError和程序抛的Exception error



## stop_func

每次抛出异常时都会执行的函数，如果和**stop_max_delay**、**stop_max_attempt_number**配合使用，则后两者会失效






