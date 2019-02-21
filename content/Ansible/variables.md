---
title: "variables"
date: 2019-02-20 17:47
---


[TOC]



# 变量

## 变量命名

变量名仅能由字母、数字和下划线组成，且只能以字母开头。



## facts 主机信息

facts是由正在通信的远程目标主机发回的信息，这些信息被保存在ansible变量中。要获取指定的远程主机所支持的所有facts，可使用如下命令进行：

```
ansible hostname -m setup
```



## register

把任务的输出定义为变量，然后用于其他任务，实例如下：

```
tasks:
   - shell: /usr/bin/foo
     register: foo_result
     ignore_errors: True
```



## 通过命令行传递变量

在运行playbook的时候也可以传递一些变量供playbook使用，示例如下：

```
ansible-playbook test.yml --extra-vars "hosts=www user=xurick"
```



## 通过roles传递变量

当给一个主机应用角色的时候可以传递变量，然后在角色内使用这些变量，示例如下：

```
- hosts: webserver
 roles:
   - common
   - {role: foo_app_instance, dir: '/web/htdocs/a.com', port: 8080}
```



## Inventory

ansible的主要功用在于批量主机操作，为了便捷的使用其中的部分主机，可以在inventory file中将其分组命名，默认的inventory file为`/etc/ansible/hosts`

inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成。

### inventory文件格式

inventory文件遵循INI文件风格，中括号中的字符为组名。可以将同一个主机同时归并到多个不同的组中；此外，当如若目标主机使用非默认的SSH端口，还可以在主机名称之后使用冒号加端口号来表明。

```
ntp.xurick.com

[webserver]
www1.xurick.com:2222
www2.xurick.com

[dbserver]
db1.xurick.com
db2.xurick.com
db3.xurick.com

如果主机名遵循相似的命名模式，还可使用列表的方式标识个主机，例如：
[webserver]
www[01:50].example.com

[databases]
db-[a:f].example.com
```



### 主机变量

可以在inventory中定义主机时为其添加主机变量以便于在playbook中使用，例如：

```
[webserver]
www1.xurick.com http_port=80 maxRequestsPerChild=808
www2.xurick.com http_port=8080 maxRequestsPerChild=909
```

### 

### 组变量

组变量是指赋予给指定组内所有主机上的在playbook中可用的变量。例如：

```
[webserver]
www1.xurick.com
www2.xurick.com

[webserver:vars]
ntp_server=ntp.xurick.com
nfs_server=nfs.xurick.com
```



### 组嵌套

inventory中，组还可以包含其它的组，并且也可以向组中的主机指定变量。不过，这些变量只能在ansible-playbook中使用，而ansible不支持。例如：

```
[apache]
httpd1.xurick.com
httpd2.xurick.com

[nginx]
ngx1.xurick.com
ngx2.xurick.com

[webserver:children]    #固定格式
apache
nginx

[webserver:vars]
ntp_server=ntp.xurick.com
```



### inventory参数

ansible基于ssh连接inventory中指定的远程主机时，还可以通过参数指定其交互方式，这些参数如下所示：

```
ansible_ssh_host
ansible_ssh_port
ansible_ssh_user
ansible_ssh_pass
ansible_sudo_pass
ansible_connection
ansible_ssh_private_key_file
ansible_shell_type
ansible_python_interpreter
```





### 条件测试

如果需要根据变量、facts或此前任务的执行结果来做为某task执行与否的前提时要用到条件测试。

#### when语句

在task后添加when字句即可使用条件测试；when语句支持jinja2表达式语句，例如：

```
tasks:
 - name: 'shutdown debian flavored system"
   command: /sbin/shutdown -h now
   when: ansible_os_family == "Debian"
```



when语句中还可以使用jinja2的大多"filter",例如果忽略此前某语句的错误并基于其结果(failed或success)运行后面指定的语句，可使用类似如下形式；



```
tasks:
 - command:/bin/false
   register: result
   ignore_errors: True
 - command: /bin/something
   when: result|failed
 - command: /bin/something_else
   when: result|success
 - command: /bin/still/something_else
   when: result|skipped
```



此外，when语句中还可以使用facts或playbook中定义的变量

```
# cat cond.yml 
- hosts: all
 remote_user: root
 vars:
 - username: user10
 tasks:
 - name: create {{ username }} user
   user: name={{ username }} 
   when: ansible_fqdn == "node1.exercise.com"
```



### 迭代

当有需要重复性执行的任务时，可以使用迭代机制。其使用格式为将需要迭代的内容定义为item变量引用，并通过with_items语句来指明迭代的元素列表即可。例如：



```
- name: add server user
 user: name={{ item }} state=persent groups=wheel
 with_items:
   - testuser1
   - testuser2
```

上面语句的功能等同于下面的语句：

```
- name: add user testuser1
 user: name=testuser1 state=present group=wheel
- name: add user testuser2
 user: name=testuser2 state=present group=wheel
```





事实上，with_items中可以使用元素还可为hashes，例如：

```
- name: add several users
 user: name={{ item.name}} state=present groups={{ item.groups }}
 with_items:
   - { name: 'testuser1', groups: 'wheel'}
   - { name: 'testuser2', groups: 'root'}
```



Ansible的循环机制还有更多的高级功能，具体请参考官方文档http://docs.ansible.com/playbooks_loops.html

