---
title: "ssh-keygen"
date: 2018-09-05 15:44
---

[TOC]

# ssh-keygen

这个问题 Enter file in which to save the key 路径要写绝对路径Á



## Usage

```
ssh-keygen [-q] [-b bits] [-t dsa | ecdsa | ed25519 | rsa] [-N new_passphrase] [-C comment] [-f output_keyfile]
```



## options

### -t 类型

类型 dsa | ecdsa | ed25519 | rsa



### -b

bit 位长度



### -E 指纹hash

默认为SHA256

#### SHA

```
ssh-keygen  -lf ~/.ssh/id_rsa_xuxuehua.pub
2048 SHA256:1kM5dwYyZccRGl7LtIfUq7lgdqb43YyW2hZqsf8sO0w  (RSA)
```



#### MD5

```
ssh-keygen  -E md5 -lf ~/.ssh/id_rsa_xuxuehua.pub
2048 MD5:d2:56:1b:d6:0d:68:9c:55:f6:65:1b:26:6d:30:02:01  (RSA)
```



#### SHA1

```
ssh-keygen  -E sha1 -lf ~/.ssh/id_rsa_xuxuehua.pub
2048 SHA1:O3p96Tu0ZPzB//LPwrTM1YyW6fc  (RSA)
```





### -y public key 

`-y` outputs the public key:

```
ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub
```



### -N 新建 passphrase

清空passphrase

```
ssh-keygen -t rsa -N '' -f test_rsa_key
```



### -p 修改passphrase

```
ssh-keygen -p -f keyfile
```





### -f 指定key 文件

```
ssh-keygen -p -f keyfile
```



