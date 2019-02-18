---
title: "configparser"
date: 2019-02-01 22:39
---


[TOC]



# configparser

在systemd的配置文件中，将section（中括号）作为key，section中存储着键值对组成字典，做成嵌套的字典



## 类 ConfigParser

```
from configparser import ConfigParser

filename = 'test.ini'
newfilename = 'mysql.ini'

cfg = ConfigParser()
cfg.read(filename)
print(cfg.sections())
print(cfg.has_section('client'))
print('-'*8)

for k, v in cfg.items():
    print(k)
    print(cfg.items(k))

if cfg.has_section('Install'):
    cfg.remove_section('Install')

cfg.add_section('test')
cfg.set('test', 'test1', '1')
cfg.set('test', 'test2', '2')


with open(newfilename, 'w') as f:
    cfg.write(f)
    
>>>
['Unit', 'Service', 'Install']
False
--------
DEFAULT
[]
Unit
[('description', 'ipa_list')]
Service
[('type', 'simple'), ('execstart', '/root/.pyenv/versions/env3.5.3/bin/python /root/rtmbot_test/ipa_list.py'), ('workingdirectory', '/root/rtmbot_test'), ('restart', 'on-failure'), ('limitcore', 'infinity')]
Install
[('wantedby', 'multi-user.target')]
```



生成后的配置文件

```
[Unit]
description = ipa_list

[Service]
type = simple
execstart = /root/.pyenv/versions/env3.5.3/bin/python /root/rtmbot_test/ipa_list.py
workingdirectory = /root/rtmbot_test
restart = on-failure
limitcore = infinity

[test]
test1 = 1
test2 = 2
```

