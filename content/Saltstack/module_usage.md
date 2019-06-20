---
title: "module_usage"
date: 2019-06-19 15:16
---
[TOC]



# 模块



## list_modules 	所有module组件列表

```
# salt \* sys.list_modules
192.168.1.11:
    - acl
    - aliases
    - alternatives
    - apache
    - archive
    - artifactory
    - blockdev
    - btrfs
    - buildout
    - cloud
    - cmd
    - composer
    - config
    - container_resource
    - cp
    - cron
    - data
    ......
```





## list_functions 	指定module的所有function方法

```
[root@localhost salt]# salt \* sys.list_functions cmd
192.168.1.11:
    - cmd.exec_code
    - cmd.exec_code_all
    - cmd.has_exec
    - cmd.retcode
    - cmd.run
    - cmd.run_all
    - cmd.run_chroot
    - cmd.run_stderr
    - cmd.run_stdout
    - cmd.script
    - cmd.script_retcode
    - cmd.shell
    - cmd.shells
    - cmd.tty
    - cmd.which
    - cmd.which_bin
```





## sys.doc	指定module用法

```
[root@localhost salt]# salt \* sys.doc cmd
'cmd.exec_code:'

    Pass in two strings, the first naming the executable language, aka -
    python2, python3, ruby, perl, lua, etc. the second string containing
    the code you wish to execute. The stdout will be returned.

    CLI Example:

        salt '*' cmd.exec_code ruby 'puts "cheese"'
```



多个module之间通过逗号分隔

```
[root@localhost salt]# salt \* sys.doc test.echo, cmd.run
'cmd.run:'

    Execute the passed command and return the output as a string

    Note that ``env`` represents the environment variables for the command, and
    should be formatted as a dict, or a YAML string which resolves to a dict.
```



## 

# 常用函数

## cp.get_file	复制文件到minion

```
[root@kvmserver data]# salt 'web9' cp.get_file salt://dev/data/zabbix_agentd_install.sh  /home/shell/zabbix_agent_install.sh
web9:
```



## cp.get_dir	复制目录到minion

```
[root@kvmserver data]# salt 'web9' cp.get_dir  salt://dev/data  /home/shell/
web9:
    - /home/shell//data/zabbix_agentd_install.sh
```



## cmd.script	批量执行脚本

在远程服务器上执行shell脚本使用

```
[root@21yunwei shell]# salt  "*"  cmd.script  salt://shell/etcbak.sh
192.168.1.11:
    ----------
    pid:
        422
    retcode:
        0
    stderr:
        tar: Removing leading `/' from member names
    stdout:
        etc bakup sucess
```



# Rerun组件

**Return组件可以理解为SaltStack系统对执行Minion返回后的数据进行存储或者返回给其他程序**，它支持多种存储方式，比如用MySQL、MongoDB、Redis、Memcache等，通过Return我们可以对SaltStack的每次操作进行记录，对以后日志审计提供了数据来源。目前官方已经支持30种Return数据存储与接口，我们可以很方便地配置与使用它。当然也支持自己定义的Return。在选择和配置好要使用的Return后，只需在salt命令后面指定Return即可。



## example

### minion 配置文件

redis方式

```
wget –no-check-certificate https://pypi.python.org/packages/source/r/redis/redis-2.8.0.tar.gz
tar -zvxf redis-2.8.0.tar.gz
mv redis-2.8.0 python-redis-2.8.0
cd python-redis-2.8.0
python setup.py install
```



```
[root@localhost python-redis-2.8.0]# python -c 'import redis; print redis.VERSION'
(2, 8, 0)
```

> 显示2.8.0 表示安装完成