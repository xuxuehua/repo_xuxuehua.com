---
title: "simplejson"
date: 2019-06-20 10:14
---
[TOC]

# simplejson



[`simplejson`](https://simplejson.readthedocs.io/en/latest/#module-simplejson) exposes an API familiar to users of the standard library `marshal` and `pickle` modules. It is the externally maintained version of the `json` library contained in Python 2.6, but maintains compatibility with Python 2.5 and (currently) has significant performance advantages, even without using the optional C extension for speedups. [`simplejson`](https://simplejson.readthedocs.io/en/latest/#module-simplejson) is also supported on Python 3.3+.



## Encoding

```
In [1]: import simplejson as json

In [2]: json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
Out[2]: '["foo", {"bar": ["baz", null, 1.0, 2]}]'

In [3]: print(json.dumps({'c': 0, 'b': 0, 'a': 0}, sort_keys=True))
{"a": 0, "b": 0, "c": 0}
```



## Pretty printing

```
In [4]: print(json.dumps({'4': 5, '6': 7}, sort_keys=True, indent=4 * ' '))
{
    "4": 5,
    "6": 7
}
```



## Decoding

```
In [8]: obj = [u'foo', {u'bar': [u'baz', None, 1.0, 2]}]

In [9]: json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]') == obj
Out[9]: True
```

