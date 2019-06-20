---
title: "states_management"
date: 2019-06-19 13:40
---
[TOC]



# States

Salt使用State模块文件进行配置管理，使用YAML编写，以.sls结尾

在salt中可以通过salt://代替根路径，例如你可以通过salt://top.sls访问/srv/salt/top.sls

如果进行配置管理首先需要再Master的配置文件中指定”file roots”的选项，Salt支持环境的配置，比如开发环节、测试环境、生产环境，但是base环境是必须的。而且Base环境必须包含入口文件top.sls。

一个简单的sls文件

```
apache:
  pkg.installed
  	- httpd
  	- httpd-devel
  service.running
  - require:
    - pkg: apache
```

> 此SLS数据确保叫做”apache”的软件包(package)已经安装,并且”apache”服务(service)正在运行中



## 激活目录

file_roots ： 设置状态文件的位置。 需要/etc/salt/master 先启用这个目录，并设置自己目录。

env环境 。base环境和开发、测试、预生产、生产环境，我们可以配置文件给不同的环境指定不同的目录。然后到/srv/salt 下边针对性创建这些目录。

```
file_roots:
base:              默认环境。
- /srv/salt    

dev:
- /srv/salt/dev    开发环境

test:
- /srv/salt/test   测试环境（功能测试、性能测试）

prod:
- /srv/salt/prod   生产环境
```



# State 模块控制

```
[root@Master salt]# cat top.sls 
base:
  '*':
    - init.dns
```

```
[root@Master init]# cat dns.sls 
/etc/resolv.conf:
  file.managed:
    - source: salt://init/resolv.conf
    - user: root
    - group: root
    - mode: 644
```



## file	管理文件状态

### file.managed

保证文件存在并且为对应的状态，不一样就根据source指定文件进行修改。比如上边的resolv.conf。

```
/etc/http/conf/http.conf:
  file.managed:
    - source: salt://apache/http.conf
    - user: root
    - group: root
    - mode: 644
    - template: jinja
    - defaults:
        custom_var: "default value"
        other_var: 123
{% if grains['os'] == 'Ubuntu' %}
    - context:
        custom_var: "override"
{% endif %}
```



### file.recurse

保证目录存在并且为对应状态，否则change。



### file.absent

确保文件不存在，如果存在就删除。



## pkg  管理软件包状态

### pkg.installed

确保软件包已经安装，如果没有安装就安装。

```
dotdeb.repo:
  pkgrepo.managed:
    - humanname: Dotdeb
    - name: deb http://packages.dotdeb.org wheezy-php55 all
    - dist: wheezy-php55
    - file: /etc/apt/sources.list.d/dotbeb.list
    - keyid: 89DF5277
    - keyserver: keys.gnupg.net
    - refresh_db: true
 
php.packages:
  pkg.installed:
    - fromrepo: wheezy-php55
    - pkgs:
      - php5-fpm
      - php5-cli
      - php5-curl
```



### pkg.remove

确保软件包已卸载，如果还是安装的，就卸载。



### pkg.purge

除remove外，还会删除其配置文件。



### pkg.latest

确保软件包是最近版本，如果不是就升级。





## service 管理服务状态

### service.running

确保服务处理运行状态，如果没有运行就启动。

```
redis:
  service.running:
    - enable: True
    - reload: True
    - watch:
      - pkg: redis
```



### service.dead

确保服务没有在运行，如果运行就停止。



### service.enabled

设置服务保持开机启动。False或True



### service.disabled

设置服务不开机启动。



## cmd 远程执行命令





## state之间的关系

每个state之间有可能涉及到达一些依赖，我们需要sls做一些指定。就好比我们要yum一个[mysql](http://www.21yunwei.com/archives/category/database/mysql)-server，需要gcc先安装。


### require/require_in 

require依赖某个状态

require_in 被某个状态所依赖



### watch/watch_in

watch关注某个状态

watch_in被某个状态所关注





### unless/onlyif





# 常用方法



## 目录管理

服务器端配置， 并配置入口文件top.sls

```
# cat /srv/salt/top.sls
base:
  'nb1':
    - apache
  'nb2':
    - filetest
```



```
# cat /srv/salt/filetest.sls
file-test:
  file.managed:
    - name: /tmp/filetest.txt
    - source: salt://test/123/1.txt
    - user: root
    - group: root
    - mode: 644
```



```
# mkdir -p /srv/salt/test/123/
# echo "file test" > /srv/salt/test/123/1.txt
# salt 'nb2' state.highstate
```





## 远程判断执行

服务器端配置

```
# cat /srv/salt/top.sls
base:
  'nb1':
    - cmdtest
```



```
# cat /srv/salt/cmdtest.sls
cmd-test:  
  cmd.run:
    - onlyif: test -f /tmp/1.txt
    - names:
      - touch /tmp/cmdtest.txt
      - mkdir /tmp/cmdtest
    - user: root
```

> 条件 onlyif 表示若 /tmp/1.txt文件存在，则执行后面的命令；可以使用 unless，两者正好相反。



客户端配置

```
# cat /tmp/1.txt 
hello
```



服务端调用

```
# salt '*' state.highstate
```



## 远程执行脚本

服务器端配置

```
# cat /srv/salt/top.sls
base:
  'nb2':
    - shelltest
```



```
# cat /srv/salt/shelltest.sls
shell-test:
  cmd.script:
    - source: salt://test/1.sh
    - user: root
```



```
# cat /srv/salt/test/1.sh
#!/bin/bash
touch /tmp/shelltest.txt
if [ -d /tmp/shelltest ]
then
    rm -rf /tmp/shelltest
else
    mkdir /tmp/shelltest
fi
```



```
# salt '*' state.highstate
```





## 计划任务

### 添加计划任务

服务器端配置

```
# cat /srv/salt/top.sls
base:
  'nb1':
    - crontest
```



```
# cat /srv/salt/crontest.sls
cron-test:
  cron.present:
    - name: /bin/touch /tmp/111.txt
    - user: root
    - minute: '*'
    - hour: 20
    - daymonth: 1-10
    - month: '3,5'
    - dayweek: '*'
```

> `*`需要用单引号引起来。当然我们还可以使用 file.managed 模块来管理 cron，因为系统的 cron都是以配置文件的形式存在的。



```
# salt '*' state.highstate
```



客户端查看

```
# crontab -l 
# Lines below here are managed by Salt, do not edit
# SALT_CRON_IDENTIFIER:/bin/touch /tmp/111.txt
* 20 1-10 3,5 * /bin/touch /tmp/111.txt
```



### 删除计划任务

服务器端配置

```
# cat /srv/salt/top.sls
base:
  'nb1':
    - crontest
```



```
# cat /srv/salt/crontest.sls
cron-test:
  cron.absent:
    - name: /bin/touch /tmp/111.txt
    - user: root
    - minute: '*'
    - hour: 20
    - daymonth: 1-10
    - month: '3,5'
    - dayweek: '*'
```

> 把 cron.present: 改成 cron.absent:
> 注意：两者不能共存，要想删除一个 cron，那之前的 present 就得替换掉或者删除掉。

```
# salt '*' state.highstate
```



# example



## dns

```
/etc/resolv.conf:
  file.managed:
    - source: salt://init/resolv.conf
    - user: root
    - group: root
    - mode: 644
```



## lamp

模块是禁止开头写的，所以要空一行

```
lamp-pkg-install:
  pkg.installed:
    - names:
      - php
      - php-cli
      - php-common
      - mysql
      - php-mysql
      - php-pdo
apache-service:
  pkg.installed:
    - name: httpd
  file.managed:
    - name: /etc/httpd/conf/httpd.conf
    - source: salt://files/httpd.conf
    - user: root
    - group: root
    - mode: 644
    - require:
      - pkg: apache-service
  service.running:
    - name: httpd
    - enable: True
    - reload: True
    - watch:
      - file: apache-service
mysql-service:
  pkg.installed:
    - name: mysql-server
    - require_in:
      - file: mysql-service
  file.managed:
    - name: /etc/my.cnf
    - source: salt://files/my.cnf
    - user: root
    - group: root
    - mode: 644
    - watch_in:
      - service: mysql-service
  service.running:
    - name: mysqld
    - enable: True
```





## init template

top.sls

```
base:
  '*':
    - init.init
```

init.sls

```
include:
  - init.dns
  - init.history
  - init.redis
  - init.zabbix-agent
```



history.sls

```
/etc/profile:
  file.append:
    - text:
      - export HISTTIMEFORMAT="%Y-%m-%d_%H:%M:%S `whoami` "
```



zabbix-agent.sls

```
zabbix-service:
  pkg.installed:
    - name: zabbix-agent
  file.managed:
    - name: /etc/zabbix_agentd.conf
    - source: salt://init/files/zabbix_agentd.conf
    - template: jinja
    - defaults:
      Zabbix_Server: {{ pillar['zabbix-agent']['Zabbix_Server'] }}
    - require:
      - pkg: zabbix-service
  service.running:
    - name: zabbix-agentd
    - enable: True
    - watch:
      - file: zabbix-service
```



