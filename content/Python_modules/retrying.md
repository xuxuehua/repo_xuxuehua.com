---
title: "retrying"
date: 2019-07-12 14:44
---
[TOC]



# retrying (Discontinued)

Please try https://github.com/jd/tenacity





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



## stop_max_attempt_number 最大尝试次数

用来设定最大的尝试次数，超过该次数就停止重试

最后一次如果还是有异常则会抛出异常，停止运行，默认为5次

```
@retry(stop_max_attempt_number=7)
def stop_after_7_attempts():
    print "Stopping after 7 attempts"
```



## stop_max_delay  函数执行超时

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





## wait_fixed 停留时间

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







## wait_random_min/wait_random_max 随机时间

用随机的方式产生两次retrying之间的停留时间

在两次调用方法停留时长，停留最短时间，默认为0

```
@retry(wait_random_min=1000, wait_random_max=2000)
def wait_random_1_to_2_s():
    print "Randomly wait 1 to 2 seconds between retries"
```



## wait_incrementing_increment

每調用一次則會增加的時長，默認 100毫秒

```
from retrying import retry
import time


@retry(stop_max_attempt_number=50, wait_fixed=1000, wait_incrementing_increment=7000)
def run():
    start_time = time.perf_counter()
    print('start_time', start_time)
    print('start run')
    raise NameError


if __name__ == "__main__":
    run()

>>>
start_time 0.051161544
start run
start_time 1.055089371
start run
start_time 8.058492401
start run
start_time 22.063655449
start run
start_time 43.067897729
start run
start_time 71.068411969
start run
start_time 106.071816208
start run
start_time 148.075782706
start run
```





## wait_exponential_multiplier/wait_exponential_max

以指数的形式产生两次retrying之间的停留时间，产生的值为2^previous_attempt_number * wait_exponential_multiplier，previous_attempt_number是前面已经retry的次数，如果产生的这个值超过了wait_exponential_max的大小，那么之后两个retrying之间的停留值都为wait_exponential_max

```
@retry(wait_exponential_multiplier=1000, wait_exponential_max=10000)
def wait_exponential_1000():
    print "Wait 2^x * 1000 milliseconds between each retry, up to 10 seconds, then 10 seconds afterwards"
```



## retry_on_exception

指定一个函数，如果此函数返回指定异常，则会重试，如果不是指定的异常则会退出

不依赖主函数中的异常重试

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
	return True

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
start run
exec run2
start run
exec run2
start run
start run
```



```
def retry_if_result_none(result):
    """Return True if we should retry (in this case when result is None), False otherwise"""
    return result is None

@retry(retry_on_result=retry_if_result_none)
def might_return_none():
    print "Retry forever ignoring Exceptions with no wait if return value is None"
```





## wrap_exception

参数设置为True/False，如果指定的异常类型，包裹在RetryError中，会看到RetryError和程序抛的Exception error

```
def retry_if_io_error(exception):
    """Return True if we should retry (in this case when it's an IOError), False otherwise"""
    return isinstance(exception, IOError)

@retry(retry_on_exception=retry_if_io_error)
def might_io_error():
    print "Retry forever with no wait if an IOError occurs, raise any other errors"

@retry(retry_on_exception=retry_if_io_error, wrap_exception=True)
def only_raise_retry_error_when_not_io_error():
    print "Retry forever with no wait if an IOError occurs, raise any other errors wrapped in RetryError"
```



## stop_func

每次抛出异常时都会执行的函数，如果和**stop_max_delay**、**stop_max_attempt_number**配合使用，则后两者会失效






