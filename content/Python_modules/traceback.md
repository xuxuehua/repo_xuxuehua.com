---
title: "traceback"
date: 2019-10-31 00:56
---
[TOC]



# traceback

获取完整错误路径

Logging the full stacktrace
A best practice is to have a logger set up for your module. It will know the name of the module and be able to change levels (among other attributes, such as handlers)

```
import logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
```

And we can use this logger to get the error:

```
try:
    do_something_that_might_error()
except Exception as error:
    logger.exception(error)
```

Which logs:

```
ERROR:__main__:something bad happened!
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "<stdin>", line 2, in do_something_that_might_error
  File "<stdin>", line 2, in raise_error
RuntimeError: something bad happened!
```

And so we get the same output as when we have an error:

```
>>> do_something_that_might_error()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in do_something_that_might_error
  File "<stdin>", line 2, in raise_error
RuntimeError: something bad happened!
```

Getting just the string
If you really just want the string, use the traceback.format_exc function instead, demonstrating logging the string here:

```
import traceback
try:
    do_something_that_might_error()
except Exception as error:
    just_the_string = traceback.format_exc()
    logger.debug(just_the_string)

```

Which logs:

```
DEBUG:__main__:Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "<stdin>", line 2, in do_something_that_might_error
  File "<stdin>", line 2, in raise_error
RuntimeError: something bad happened!
```
