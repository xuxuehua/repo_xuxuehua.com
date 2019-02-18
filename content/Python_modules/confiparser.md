---
title: "confiparser"
date: 2018-08-26 19:10
---

[TOC]

# ConfigParser

用于生成和修改常见配置文档，当前模块的名称在 python 3.x 版本中变更为 configparser。



## 生成配置文件

来看一个好多软件的常见文档格式如下

```
[DEFAULT]
ServerAliveInterval = 45
Compression = yes
CompressionLevel = 9
ForwardX11 = yes
 
[bitbucket.org]
User = hg
 
[topsecret.server.com]
Port = 50022
ForwardX11 = no
```



```
import configparser
 
config = configparser.ConfigParser()
config["DEFAULT"] = {'ServerAliveInterval': '45',
                      'Compression': 'yes',
                     'CompressionLevel': '9'}
 
config['bitbucket.org'] = {}
config['bitbucket.org']['User'] = 'hg'
config['topsecret.server.com'] = {}
topsecret = config['topsecret.server.com']
topsecret['Host Port'] = '50022'     # mutates the parser
topsecret['ForwardX11'] = 'no'  # same here
config['DEFAULT']['ForwardX11'] = 'yes'
with open('example.ini', 'w') as configfile:
   config.write(configfile)
```



## 读取配置文件

```
>>> import configparser
>>> config = configparser.ConfigParser()
>>> config.sections()
[]
>>> config.read('example.ini')
['example.ini']
>>> config.sections()
['bitbucket.org', 'topsecret.server.com']
>>> 'bitbucket.org' in config
True
>>> 'bytebong.com' in config
False
>>> config['bitbucket.org']['User']
'hg'
>>> config['DEFAULT']['Compression']
'yes'
>>> topsecret = config['topsecret.server.com']
>>> topsecret['ForwardX11']
'no'
>>> topsecret['Port']
'50022'
>>> for key in config['bitbucket.org']: print(key)
...
user
compressionlevel
serveraliveinterval
compression
forwardx11
>>> config['bitbucket.org']['ForwardX11']
'yes'
```







## 改

```
[section1]
k1 = v1
k2:v2
  
[section2]
k1 = v1
 
import ConfigParser
  
config = ConfigParser.ConfigParser()
config.read('i.cfg')

########## 改写 ##########
sec = config.remove_section('group1')
config.write(open('i.cfg', "w"))
  
sec = config.has_section('wupeiqi')
sec = config.add_section('wupeiqi')
config.write(open('i.cfg', "w"))
  
  
config.set('group2','k1',11111)
config.write(open('i.cfg', "w"))
  
config.remove_option('group2','age')
config.write(open('i.cfg', "w"))
```



## 查

```
[section1]
k1 = v1
k2:v2
  
[section2]
k1 = v1
 
import ConfigParser
  
config = ConfigParser.ConfigParser()
config.read('i.cfg')

########## 读 ##########
secs = config.sections()
print secs
options = config.options('group2')
print options
  
item_list = config.items('group2')
print item_list
  
val = config.get('group1','key')
val = config.getint('group1','key')
```

