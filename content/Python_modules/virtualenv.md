---
title: "virtualenv"
date: 2020-06-29 02:09
---
[toc]



# virtualenv



## installation

```
pip install virtualenv
```



## hello world

```
$ virtualenv my_venv

$ source my_venv/bin/activate

$ deactivate
```





## -p / --python 指定版本

target interpreter for which to create a virtual (either absolute path or identifier string)

```
virtualenv --python=/usr/bin/python2.6 <path/to/new/virtualenv/>
```



# Install specific python

```
cd /tmp
wget https://www.python.org/ftp/python/3.7.6/Python-3.7.6.tar.xz
tar -xf Python-3.7.6.tar.xz
cd Python-3.7.6
mkdir /root/id_generator/.localpython
./configure --prefix=/root/id_generator/.localpython
make && make install
```



```
cd /tmp
wget https://www.python.org/ftp/python/3.7.6/Python-3.7.6.tar.xz
tar -xf Python-3.7.6.tar.xz
cd Python-3.7.6
mkdir /tmp/id_generator/.localpython
./configure --prefix=/tmp/id_generator/.localpython
make && make install
```



