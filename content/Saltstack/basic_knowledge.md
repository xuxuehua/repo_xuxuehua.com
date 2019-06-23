---
title: "basic_knowledge"
date: 2019-06-18 08:53
---
[TOC]



# SaltStack

SaltStack是基础设施管理的一种具有革命性意义的方法，以速度替代复杂性。SaltStack足够简单，可以在几分钟内运行，可扩展到足以管理数万台服务器，并且能够在几秒钟内快速与每个系统进行通信。

底层采用动态的连接总线（ZeroMQ消息队列pub/sub方式通信），使用ssl证书签发的方式进行认证管理



## installation

### centos

#### master

```
yum -y install salt-master salt-minion
systemctl start salt-master
systemctl enable salt-master
```



#### minion

```
yum -y install salt-minion

sed -i 's/#master: salt/master: IPADDRESS/g' /etc/salt/minion

systemctl start salt-minion
systemctl enable salt-minion
```



### Source code

#### master

```
git clone https://github.com/saltstack/salt.git

pip install -r salt/requirements/zeromq.txt

python salt/setup.py install

mkdir /etc/salt/; cp salt/conf/master /etc/salt/

salt-master -d
```



#### minion

```
git clone https://github.com/saltstack/salt.git

pip install -r salt/requirements/zeromq.txt

python salt/setup.py installSalt

mkdir /etc/salt/; cp salt/conf/minion /etc/salt/

sed -i 's/#master: salt/master: IPADDRESS/g' /etc/salt/minion  #IPADDRESS为Master服务器地址

salt-minion -d
```



### pip

#### master

```
pip install salt

mkdir /etc/salt/;wget -SO /etc/salt/master https://github.com/saltstack/salt/blob/develop/conf/master

salt-master -d
```





#### minion

```
pip install salt

mkdir /etc/salt/;wget -SO /etc/salt/minion https://github.com/saltstack/salt/blob/develop/conf/minion

sed -i 's/#master: salt/master: IPADDRESS/g' /etc/salt/minion  #IPADDRESS为Master服务器地址

salt-minion -d
```



### salt-bootstrap 

salt-bootstrap是SaltStack的一个单独项目，该项目主要用于解决多平台一键部署SaltStack环境。核心工程就是维护一个庞大的bash脚本



#### master

```
Wget -c https://raw.githubusercontent.com/saltstack/salt-bootstrap/stable/bootstrap-salt.sh —no-check-certificate  

wget -O [bootstrap-salt.sh](http://bootstrap-salt.sh) https://bootstrap.saltstack.com 

sudo sh [bootstrap-salt.sh](http://bootstrap-salt.sh) 
```



#### minion

只安装最新版Minion并且指定Minion id关于salt-bootstrap脚本的参数大家可以运行sh install_salt.sh -h查看，该脚本也提供非常方便的一键部署参数。

```
echo "IPADDRESS     salt"  &gt;&gt; /etc/hosts  #IPADDRESS为Master服务器地址

curl -L https://bootstrap.saltstack.com -o 
install_salt.sh

sh install_salt.sh -i Minion  
```



# 架构



![img](https://snag.gy/ymdafR.jpg)



## Master -> Minion   

master 和minion 全部直连 



### 配置及参数

4505 (publish_port) Salt Master pub 接口 

可以通过修改/etc/salt/master配置文件的publish_port参数设置。它是salt的消息发布系统，如果查看4505端口，会发现所有的Minion连接到Master的4505端口，TCP状态持续保持为ESTABLISHED。





4506 (ret_port) Salt-Master Ret 接口 

ZeroMQ REP系统，默认监听4506端口，可以通过修改/etc/salt/master配置文件的ret_port参数设置。它是salt客户端与服务端通信的端口。比如说Minion执行某个命令后的返回值就是发送给Master的4506这个REP端口



user: 指定master 进程的运⾏⽤户,  如果调整,  则需要调整部分目录的权限(默认为root)
timeout:  指定timeout时间,  如果minion规模庞⼤或⺴络状况不好,建议增⼤该值(默认5s)
keep_jobs:  默认情况下, minion 会执⾏结果会返回master, master会缓存到本地的cachedir 目录,  该参数指定缓存多⻓时间,  以供查看之前的执⾏结果,  会占⽤磁盘空间(默认为24h)
job_cache: master 是否缓存执⾏结果,  如果规模庞⼤(超过5000台),  建议使⽤其他⽅式来存储jobs,  关闭本选项(默认为True)
file_recv :  是否允许minion传送⽂件到master 上(默认是Flase)
file_roots: 指定file server目录,  默认为:

```
file_roots:    
  base:    
  - /srv/salt     
```



指定pillar 目录,  默认为:log_level:  执⾏⽇志级别,  ⽀持的⽇志级别有'garbage', 'trace', 'debug', info', 'warning', 'error', ‘critical ’ ( 默认为’warning’)

```
pillar_roots
  base:
    - /srv/pillar  
```





### 配置文件 

```
/etc/salt/master 
/etc/salt/minion 
```



### 进程 

```
/usr/bin/python /usr/bin/salt-master -d 
/usr/bin/python /usr/bin/salt-minion -d  
```



### 管理密钥

Salt-key 命令来管理minion 密钥 

```
[root@server ~]# salt-key  -L 
Accepted Keys: 
Denied Keys: 
Unaccepted Keys: 
minion-one 
Rejected Keys: 
```





master上接受密钥 

```
[root@localhost ~]# salt-key -L 
Accepted Keys:
Denied Keys:
Unaccepted Keys:
192.168.1.11
Rejected Keys:
[root@localhost ~]# salt-key -A -y 
The following keys are going to be accepted:
Unaccepted Keys:
192.168.1.11
Key for minion 192.168.1.11 accepted.
[root@localhost ~]# salt-key -L 
Accepted Keys:
192.168.1.11
Denied Keys:
Unaccepted Keys:
Rejected Keys:
```



minion在第一次启动时, 会自动生成minion.pem， 然后将 minion.pub发送给master。master在接收到minion的public key后，通过salt-key命令accept minion public key，这样在master的/etc/salt/pki/master/minions下的将会存放以minion id命名的 public key，然后master就能对minion发送指令了。



查看密钥的职位密码来确保其与minion的密钥相匹配，在master上查看 

```
[root@server ~]# salt-key -f minion-one 
Unaccepted Keys: 
minion-one:  ad:e0:1c:1f:46:c6:93:24:fb:e6:b2:f5:bc:e5:fa:b6 
```





在minion上查询minion的私钥  

```
[root@client ~]# salt-call --local key.finger 
local: ad:e0:1c:1f:46:c6:93:24:fb:e6:b2:f5:bc:e5:fa:b6 
```









#### Modules

在命令行中和配置文件中使用的指令模块,可以在命令行中运行





#### highstate

为minion端下发永久添加状态,从sls配置文件读取.即同步状态配置

触发所有minion从master下载top.sls文件以及其中定一个的states，然后编译、执行。执行完之后，minion会将执行结果的摘要信息汇报给master。

```
[root@localhost pillar]# salt '*' state.highstate
192.168.1.11:
----------
          ID: apache-service
    Function: pkg.installed
        Name: httpd
      Result: True
     Comment: The following packages were installed/updated: httpd
     Started: 04:25:07.910627
    Duration: 77592.817 ms
     Changes:   
              ----------
              apr:
                  ----------
                  new:
                      1.4.8-3.el7_4.1
                  old:
              apr-util:
                  ----------
                  new:
                      1.5.2-6.el7
                  old:
              httpd:
                  ----------
                  new:
                      2.4.6-89.el7.centos
                  old:
              httpd-tools:
                  ----------
                  new:
                      2.4.6-89.el7.centos
                  old:
              mailcap:
                  ----------
                  new:
                      2.1.41-2.el7
                  old:
----------
          ID: apache-service
    Function: pkg.installed
        Name: httpd-devel
      Result: True
     Comment: The following packages were installed/updated: httpd-devel
     Started: 04:26:25.518515
    Duration: 21131.086 ms
     Changes:   
              ----------
              apr-devel:
                  ----------
                  new:
                      1.4.8-3.el7_4.1
                  old:
              apr-util-devel:
                  ----------
                  new:
                      1.5.2-6.el7
                  old:
              cyrus-sasl:
                  ----------
                  new:
                      2.1.26-23.el7
                  old:
              cyrus-sasl-devel:
                  ----------
                  new:
                      2.1.26-23.el7
                  old:
              expat-devel:
                  ----------
                  new:
                      2.1.0-10.el7_3
                  old:
              httpd-devel:
                  ----------
                  new:
                      2.4.6-89.el7.centos
                  old:
              libdb-devel:
                  ----------
                  new:
                      5.3.21-24.el7
                  old:
              openldap:
                  ----------
                  new:
                      2.4.44-21.el7_6
                  old:
                      2.4.44-20.el7
              openldap-devel:
                  ----------
                  new:
                      2.4.44-21.el7_6
                  old:
----------
          ID: apache-service
    Function: service.running
        Name: httpd
      Result: True
     Comment: Service httpd has been enabled, and is running
     Started: 04:26:46.743870
    Duration: 612.381 ms
     Changes:   
              ----------
              httpd:
                  True

Summary
------------
Succeeded: 3 (changed=3)
Failed:    0
------------
Total states run:     3
```



#### salt_schedule

会自动保持客户端配置







## Master -> Syndic -> Minion 

 通过syndic 对Minion进行管理 

Syndic 建立在中心master 和minion之间，并允许多层分级Syndic

Syndic运行在一个Master上，并连接到另外一个Master(比她更高级别)，然后Syndic minion所连接的高级Master就可以控制连接到运行Syndic的Master 上的minion。

​    

## Masterless 无Master的minion 

只需要在每台机器上安装Minion，然后采用本机只负责对本机的配置管理工作机制服务模式

直接在本地使用salt-call命令使用Saltstack的各种功能，而不用连接到master上

```
# salt-call --local state.highstate
```









# 系统组件

## Grains

minion端的变量,静态的

Grains是SaltStack记录Minion的一些静态信息的组件，我们可以简单地理解为Grains里面记录着每台Minion的一些常用属性，比如CPU、内存、磁盘、网络信息等，我们可以通过grains.items查看某台Minion的所有Grains信息，Minions的Grains信息是Minions启动的时候采集汇报给Master的，在实际应用环境中我们需要根据自己的业务需求去自定义一些Grains



grains的特性–每次启动汇报、静态决定了它没有pillar灵活，要知道pillar是随时可变的，只要在master端修改了那一般都会立刻生效的。所以**grains更适合做一些静态的属性值的采集**，例如设备的角色(role)，磁盘个数(disk_num），操作系统版本等诸如此类非常固定的属性。



### grains优先级

通过master下发的grains优先级是最高的(/etc/salt/grains)，/etc/salt/minion次之，/etc/salt/grains最低



### grains的下发



#### 自定义

可以通过[`state.highstate`](http://docs.saltstack.com/ref/modules/all/salt.modules.state.html#salt.modules.state.highstate)、[`saltutil.sync_grains`](http://docs.saltstack.com/ref/modules/all/salt.modules.saltutil.html#salt.modules.saltutil.sync_grains)、[`saltutil.sync_all`](http://docs.saltstack.com/ref/modules/all/salt.modules.saltutil.html#salt.modules.saltutil.sync_all) 等方法批量下发，切记所有在_grains目录下的所有自定义grains值都会下发到minion，这是血的教训。

通过`state.highstate` 下发的grains好处是无须重启minion即可生效



#### 固定配置

固定存放在minion端配置文件中，如grains、minion文件中，可以通过file manager的方法去批量下发/etc/salt/grains等配置文件实现grains的批量下发，当然了也通过别的方式把这个文件批量下发下去，都是ok的。

通过下发/etc/salt/grains文件下发的grains值则必须重启minion端服务才可以生效



### -G

用于指定匹配的grains

```
salt -G 'os:CentOS' test.ping
```



### grains.items  查询grains

查询grains 信息

```
# salt \* grains.items
192.168.1.11:
    ----------
    SSDs:
    biosreleasedate:
        12/01/2006
    biosversion:
        VirtualBox
    cpu_flags:
        - fpu
```



### 自定义Grains

在Minion的/etc/salt/minion配置文件中默认有一些注释行。这里就是在Minion上
的minion配置文件中如何定义Grains信息例子。下面只需根据自动的需求按照以下格式去填写相应的键值对就行，大家注意格式就行，SaltStack的配置文件的默认格式都是YAML格式

```
# Custom static grains for this minion can be specified here and used in SLS
# files just like all other grains. This example sets 4 custom grains, with
# the 'roles' grain having two values that can be matched against.
#grains:
#  roles:
#    - webserver
#    - memcache
#  deployment: datacenter4
#  cabinet: 13
#  cab_u: 14-15
#
```



为了统一管理Minion的Grains信息，需要把这
些注释复制到minion.d/grains文件中

```
# vi /etc/salt/minion

# Custom static grains for this minion can be specified here and used in SLS
# files just like all other grains. This example sets 4 custom grains, with
# the 'roles' grain having two values that can be matched against.
grains:
  roles:
    - nginx
  env:
    - test
  myname:
    - hadron
#  deployment: datacenter4
#  cabinet: 13
#  cab_u: 14-15
```







## Pillar

Pillar用于给**特定的 minion** 定义任何你需要的数据， 这些数据可以被Salt的其他组件使用

minion端的变量, pillar 和 grains 不一样，是在 master 上定义的，并且是针对 minion 定义的一些信息。像一些比较重要的数据（密码）可以存在 pillar 里，还可以定义变量等。



### 激活pillar

```
# vim /etc/salt/master

pillar_roots:
  base:
    - /srv/pillar
```



```
mkdir /srv/pillar
```



### 自定义pillar

总入口文件，内容如下

```
[root@nb0 ~]# vim /srv/pillar/top.sls
[root@nb0 ~]# cat /srv/pillar/top.sls
base:
  'nb1':
    - test
```



自定义配置文件，内容如下

```
[root@nb0 ~]# vim /srv/pillar/test.sls
[root@nb0 ~]# cat /srv/pillar/test.sls
conf: /etc/test123.conf
myname: hadron
```



获取pillar 状态

```
[root@localhost pillar]# salt '*' saltutil.refresh_pillar
192.168.1.11:
    True
[root@localhost pillar]# salt '*' pillar.items
192.168.1.11:
    ----------
    conf:
        /etc/test123.conf
    myname:
        rick
[root@localhost pillar]# salt '*' pillar.items myname
192.168.1.11:
    ----------
    myname:
        rick
```



# example

## 部署apache



### master

```
vim /etc/salt/master

file_roots:
  base:
    - /srv/salt
```

```
cat /srv/salt/top.sls
base:
  '192.168.1.11':
    - apache
```

```
cat /srv/salt/apache.sls
apache-service:
  pkg.installed:
    - names:
      - httpd
      - httpd-devel
  service.running:
    - name: httpd
    - enable: True
```

> apache-service 是自定义的 id 名。pkg.installed 为包安装函数，下面是要安装的包的名字。service.running 也是一个函数，来保证指定的服务启动，enable 表示开机启动

### minion







