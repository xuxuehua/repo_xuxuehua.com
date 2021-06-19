---
title: "ssh-add"
date: 2019-01-21 15:04
---


[TOC]



# ssh-add



## usage

```
ssh-add [options] [file ...]
```

## Options

### -l  列出指纹

List fingerprints of all identities.



### -E 指定hash算法

Specify hash algorithm used for fingerprints.



### -L 列出公钥参数

List public key parameters of all identities.



### -k 只载入keys

Load only keys and not certificates.



### -c  

Require confirmation to sign using identities



### -t 设置超时时间

Set lifetime (in seconds) when adding identities.

### -d 删除单个身份

Delete identity.

### -D 删除所有身份

Delete all identities.

```
eval ssh-agent -s && ssh-add -D && ssh-add ~/.ssh/id_rsa && ssh-add -l && git add .
```



### -x 锁定代理

Lock agent.

### -X 解锁代理

Unlock agent.

### -s

pkcs11   Add keys from PKCS#11 provider.

### -e

pkcs11   Remove keys provided by PKCS#11 provider.

### -q

Be quiet after a successful operation.

### -A

Add all identities stored in your keychain.

### -K

Store passphrases in your keychain.

With -d, remove passphrases from your keychain.







# FAQ

## Could not open a connection to your authentication agent



#### ssh-agent

```bash
eval `ssh-agent -s` 
or
eval $(ssh-agent -s)

ssh-add
```