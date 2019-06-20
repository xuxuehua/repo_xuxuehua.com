---
title: "manage_targets"
date: 2019-06-18 15:02
---
[TOC]



# 管理对象

在SaltStack系统中我们的管理对象叫作Target，在Master上我们可以采用不同Target去管理不同的Minion。这些Target都是通过去管理和匹配Minion的ID来做的一些集合



## -E 正则匹配

```
# salt 'nb*' network.ip_addrs
nb0:
    - 192.168.1.160
nb1:
    - 192.168.1.161
nb2:
    - 192.168.1.162
```





## -L 列表匹配

-L, –list 列表匹配

```
# salt -L nb1,nb2 test.ping
nb2:
    True
nb1:
    True
```





## -G Grians 匹配

-G, –grain grains 匹配

```
# salt -G 'os:CentOS' test.ping
nb0:
    True
nb1:
    True
nb2:
    True
```

> os:CentOS，这里的对象是一组键值对，
> 这里用到了Minion的Grains的键值对



## -I Pillar 匹配

```
[root@Master ~]# salt  -I 'key:value' test.ping
webserver:
    True
```





## -N 组匹配

首先在master配置文件中定义组

```
[root@nb0 ~]# vi /etc/salt/master
1
#####         Node Groups           #####
##########################################
# Node groups allow for logical groupings of minion nodes. A group consists of a group
# name and a compound target.
#nodegroups:
#  group1: 'L@foo.domain.com,bar.domain.com,baz.domain.com and bl*.domain.com'
#  group2: 'G@os:Debian and foo.domain.com'
```

L@ 和G@ 分别表示minion和grain信息
L@开通的是指定的以逗号分隔的多个minionId

Letter	Match Type	Example	Alt Delimiter?
G	Grains glob	G@os:Ubuntu	Yes
E	PCRE Minion ID	`E@web\d+.(dev	qa
P	Grains PCRE	P@os:(RedHat	Fedora
L	List of minions	L@minion1.example.com,minion3.domain.com or bl*.domain.com	No
I	Pillar glob	I@pdata:foobar	Yes
J	Pillar PCRE	`J@pdata:^(foo	bar)$`
S	Subnet/IP address	S@192.168.1.0/24 or S@192.168.1.100	No
R	Range cluster	R@%foo.bar	No
Matchers can be joined using boolean and, or, and not operators.

修改group1:group1: 'L@nb1,nb2'

-N, –nodegroup 组匹配

```
root@saltmaster: salt -N  "webnode"  test.ping
vps-web1:
True
vps-web2:
True
```





## -S CIDR 匹配

```
# salt -S '192.168.1.0/24' test.ping
nb0:
    True
nb2:
    True
nb1:
    True
```



## -C 复合匹配

```
[root@Master pillar]# salt  -C  'G@os:CentOS and I@key:value'  test.ping
aliserver:
    True
```

