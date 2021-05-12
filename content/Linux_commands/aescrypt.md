---
title: "aescrypt"
date: 2018-11-16 08:26
---




[TOC]


# aescrypt



# Installation

## Linux 

```
wget https://www.aescrypt.com/download/v3/linux/aescrypt-3.14.tgz
```



## Mac

```
https://www.aescrypt.com/download/v3/mac/aescrypt_mac_v314_x64.zip
```



# Usage

```
aescrypt {-e|-d} [ { -p <password> | -k <keyfile> } ] { [-o <output filename>] <file> | <file> [<file> ...] }
```



## -e encrypt

```
aescrypt -e apples picture.jpg
```

```
tar -zcvf - /home | aescrypt -e -p apples - >backup_files.tar.gz.aes
```



## -d decrypt

```
aescrypt -d apples picture.jpg.aes
```



## -o decrypt on screen instead of file

```
aescrypt -d -o - 1.txt.aes
```



# aescrypt_keygen

Installed after compleing from source code



## Usage

```
aescrypt_keygen [ { -g <password length>] | [-p <password> } ] <keyfile>
```



### -g specific length

Generate a 64 length random key file named aes.key, sa

```
aescrypt_keygen  -p -g 64 aes.key
```



Encrypt 1.txt with aes.key file

```
aescrypt -e 1.txt  -k aes.key
```

