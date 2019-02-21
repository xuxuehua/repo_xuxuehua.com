---
title: "playbook"
date: 2019-02-20 18:14
---


[TOC]

# Ansible playbook

playbook是由一个或多个"play"组成的列表。play的主要功能在于将事先归并为一组的主机装扮成事先通过ansible中的task定义好的角色。从根本上来讲，所有task无非是调用ansible的一个module。将多个play组织在一个playbook中，即可以让他们连同起来按事先编排的机制同唱一台大戏。下面是一个简单示例。

```
- hosts: webserver
 vars:
   http_port: 80
   max_clients: 256
 remote_user: root
 tasks:
 - name: ensure apache is at the latest version
   yum: name=httpd state=latest
 - name: ensure apache is running
   service: name=httpd state=started
 handlers:
   - name: restart apache
     service: name=httpd state=restarted
```



## 组成结构

```
inventory       #以下操作应用的主机
modules         #调用哪些模块做什么样的操作
ad hoc commands #在这些主机上运行哪些命令
playbooks   
   tasks       #任务,即调用模块完成的某操作
   variable    #变量
   templates   #模板
   handlers    #处理器，由某事件触发执行的操作
   roles       #角色
```





## playbook基础组件

### Hosts和Users

playbook中的每一个play的目的都是为了让某个或某些主机以某个指定的用户身份执行任务。hosts用于指定要执行指定任务的主机，其可以使一个或多个由冒号分隔主机组；remote_user则用于指定远程主机的执行任务的用户，如上面的实例中的

```
- hosts: webserver
 remote_user: root
```



不过，remote_user也可用于各task中，也可以通过指定其通过sudo的方式在远程主机上执行任务，其可用于play全局或其任务；此外，甚至可以在sudo时使用sudo_user指定sudo时切换的用户。

```
- hosts: webserver
 remote_user: magedu
 tasks:
  - name: test connection
    ping:
    remote_user: magedu
    sudo: yes
```



### 任务列表和action

play的主题部分是task list。task list中的各任务按次序逐个在hosts中指定的所有主机上执行，即在所有主机上完成第一个任务后再开始第二个。在运行自上而下某playbook时，如果中途发生错误，所有已执行任务都可能回滚，在更正playbook后重新执行一次即可。

Task的目的是使用指定的参数执行模块，而在模块参数中可以使用变量。模块执行是幂等的。这意味着多次执行是安全的，因为其结果均一致。

每个task都应该有其name，用于playbook的执行结果输出，建议其内容尽可能清晰地描述任务执行步骤，如果为提供name，则action的结果将用于输出。

定义task可以使用"action: module options"或”module：options“的格式推荐使用后者以实现向后兼容。如果action一行的内容过多，也中使用在行首使用几个空白字符进行换行。



```
tasks:
 - name:make sure apache is running
   service: name=httpd state=started
```



```
tasks:
 - name: run this command and ignore the result
   shell: /usr/bin/somecommand || /bin/true
```

在众多的模块中，只有command和shell模块仅需要给定一个列表而无需使用"key=value"格式，例如：



```
tasks:
 - name: disable selinux
   command: /sbin/setenforce 0
```



如果命令或脚本的退出码不为零，可以使用ignore_errors来忽略错误信息：

```
tasks:
 - name: run this command and ignore the result
   shell: /usr/bin/somecommand
   ignore_errors: True
```



### handlers

用于当关注的资源发生变化时采取一定的操作。

"notify"这个action可用于在每个play的最后被触发，这样可以避免多次有改变发生时每次都执行执行的操作，取而代之，仅在所有的变化发生完成后一次性地执行指定操作，在notify中列出的操作称为handlers，也即notify中调用handlers中定义的操作。

```
- name: template configuration file
 template: src=template.j2 dest=/etc/foo.conf
 notify:
   - restart memcached
   - restart apache
```

handlers是task列表，这些task与前述的task并没有本质上的不同。



```
handlers：
 - name: restart memcached
   service: name=memcached state=restarted
 - name: restart apache
   service: name=apache state=restarted
```



# example

```
# cat nginx.yml 
- hosts: webserver
 remote_user: root
 tasks:
 - name: create nginx group
   group: name=nginx system=yes gid=208
 - name: create nginx user
   user: name=nginx uid=208 group=nginx system=yes

- hosts: dbserver
 remote_user: root
 tasks:
 - name: copy file to dbserver
   copy: src=/etc/inittab dest=/tmp/inittab.ans
   
# ansible-playbook nginx.yml
```



```
# cat apache.yml 
- hosts: webserver
 remote_user: root
 tasks:
 - name: install httpd package
   yum: name=httpd state=latest
 - name: install configuration file for httpd
   copy: src=/root/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
 - name: start httpd service
   service: enabled=true name=httpd state=started

# ansible-playbook apache.yml
```



## handlers 

```
# cat apache.yml 
- hosts: webserver
 remote_user: root
 tasks:
 - name: install httpd package
   yum: name=httpd state=latest
 - name: install configuration file for httpd
   copy: src=/root/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
   notify: 
   - restart httpd
 - name: start httpd service
   service: enabled=true name=httpd state=started
 
 handlers:
 - name: restart httpd
   service: name=httpd state=restarted

#  ansible-playbook apache.yml
```



## variable 

```
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
   copy: src=/root/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
   notify: 
   - restart httpd
 - name: start httpd service
   service: enabled=true name={{ service }} state=started
 
 handlers:
 - name: restart httpd
   service: name=httpd state=restarted
```





在playbook中可以使用所有的变量

```
# cat facts.yml 
- hosts: webserver
 remote_user: root
 tasks:
 - name: copy file
   copy: content="{{ ansible_all_ipv4_addresses }} " dest=/tmp/vars.ans
```