---
title: "hashlib"
date: 2018-08-26 19:22
---

[TOC]

# hashlib

用于加密相关的操作，3.x里代替了md5模块和sha模块，主要提供 SHA1, SHA224, SHA256, SHA384, SHA512 ，MD5 算法

```
import hashlib
 
m = hashlib.md5()
m.update(b"Hello")
m.update(b"It's me")
print(m.digest())
m.update(b"It's been a long time since last time we ...")
 
print(m.digest()) #2进制格式hash
print(len(m.hexdigest())) #16进制格式hash
'''
def digest(self, *args, **kwargs): # real signature unknown
    """ Return the digest value as a string of binary data. """
    pass
 
def hexdigest(self, *args, **kwargs): # real signature unknown
    """ Return the digest value as a string of hexadecimal digits. """
    pass
 
'''
import hashlib
 
# ######## md5 ########
 
hash = hashlib.md5()
hash.update('admin')
print(hash.hexdigest())
 
# ######## sha1 ########
 
hash = hashlib.sha1()
hash.update('admin')
print(hash.hexdigest())
 
# ######## sha256 ########
 
hash = hashlib.sha256()
hash.update('admin')
print(hash.hexdigest())
 
 
# ######## sha384 ########
 
hash = hashlib.sha384()
hash.update('admin')
print(hash.hexdigest())
 
# ######## sha512 ########
 
hash = hashlib.sha512()
hash.update('admin')
print(hash.hexdigest())
```



## 应用

网页防篡改，将目标网页源码hash后进行对比