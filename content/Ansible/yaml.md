---
title: "yaml"
date: 2019-02-20 17:44
---


[TOC]



# YAML

YAML是一个可读性高的用来表达资料序列的格式。YAML参考了其它多种语言，包括：XML、C语言、Python、Perl以及电子邮件格式RFC2822等。ClarkEvans在2001年首次发表了这种语言，另外Ingy dot Net与Oren Ben-Kiki也是这语言的共同设计者。

YAML Ain't Markup Language,即YAML不是XML，不过，在开发这种语言时，YAML的意思其实是："Yet Another Markup Language"(仍是一种标记语言)，其特性：

- YAML的可读性好
- YAML和脚本语言的交互性好
- YAML使用实现语言的数据类型
- YAML有一个一致的信息模型
- YAML易于实现
- YAML可以基于流来处理
- YAML表达能力强，扩展性好

更多的内容及规范参见http://www.yaml.org



## 语法

YAML的语法和其他高阶语言类似，并且可以简单表达清单、散列表、标量等数据结构，其结构(structure)通过空格来展示，序列(sequence)里的项用"-"来表示，Map里面的键值对用":"分割，下面是一个示例。



```
name: john smith
age: 41
gender: male
spouse:
   name:jane smith
   age:37
   gender: female
children:
   -   name:jimmy smith
       age:17
       gender: male
   -   name:jenny smith
       age: 13
       gender: female
```



YAML文件扩展名通常为.yaml，如example.yaml



### list 形式

列表的所有元素均使用"-"打头，例如：

```
# A list of testy fruits
- Apple
- Orange
- Strawberry
- Mango
```



### dictionary 形式

字典通过key与value进行标识，例如：

```
# An employee record
name: Example Developer
job: Developer
skill: Elite
```

也可以将key:value放置于{}中进行表示，例如：

```
#An exmloyee record
{name: Example Developer, job: Developer, skill: Elite}
```





## 模版

```
# grep '{{' conf/httpd.conf 
MaxClients       {{ maxClients }}
Listen {{ httpd_port }}

# cat /etc/ansible/hosts
[webserver]
127.0.0.1 httpd_port=80 maxClients=100
192.168.10.149 httpd_port=8080 maxClients=200

# cat apache.yml 
- hosts: webserver
 remote_user: root
 vars:
 - package: httpd
 - service: httpd
 tasks:
 - name: install httpd package
   yum: name={{ package }} state=latest
 - name: install configuration file for httpd
   template: src=/root/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
   notify: 
   - restart httpd
 - name: start httpd service
   service: enabled=true name={{ service }} state=started
 
 handlers:
 - name: restart httpd
   service: name=httpd state=restarted
```

