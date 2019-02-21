---
title: "roles"
date: 2019-02-20 18:23
---


[TOC]

# Roles

ansible自1.2版本引入的新特性，用于层次性、结构化地组织playbook。

roles能够根据层次型结构自动转载变量文件、tasks以及handlers等。

要使用roles只需要在playbook中使用include指令即可。

简单来讲，roles就是通过分别将变量、文件、任务、模板以及处理器放置于单独的目录中，并可以便捷地include他们的一种机制。角色一般用于基于主机构建服务的场景中，但也可以使用于构建守护进程的场景中



一个roles的案例如下所示：

```
site.yml
webserver.yml
fooserver.yml
roles/
   common/
       files/
       templates/
       tasks/
       handlers/
       vars/
       meta/
   webserver/
       files/
       templates/
       tasks/
       handlers/
       vars/
       meta/
```



而在playbook中，可以这样使用role：

```
- hosts: webserver
 roles:
   - common  
   - webserver
```



也可以向roles传递参数，例如：

```
- hosts: webserver
 roles:
   - common
   - { role: foo_app_instance, dir:'/opt/a',port:5000}
   - { role: foo_app_instance, dir:'/opt/b',port:5001}
```



甚至也可以条件式地使用roles，例如：

```
- hosts：webserver
 roles:
   - { role: some_role, when: "ansible_so_family == 'RedHat" }
```



## 创建role的步骤

1. 创建以roles命名的目录：
2. 在roles目录中分别创建以各角色命名的目录，如webserver等
3. 在每个角色命名的目录中分别创建files、handlers、meta、tasks、templates和vars目录；用不到的目录可以创建为空目录，也可以不创建
4. 在playbook文件中，调用各角色



## role内各目录中可应用的文件

- task目录：至少应该包含一个为main.yml的文件，其定义了此角色的任务列表；此文件可以使用include包含其它的位于此目录中的task文件；
- file目录：存放由copy或script等模板块调用的文件；
- template目录：template模块会自动在此目录中寻找jinja2模板文件；
- handlers目录：此目录中应当包含一个main.yml文件，用于定义此角色用到的各handlers，在handler中使用inclnude包含的其它的handlers文件也应该位于此目录中；
- vars目录：应当包含一个main.yml文件，用于定义此角色用到的变量
- meta目录：应当包含一个main.yml文件，用于定义此角色的特殊设定及其依赖关系；ansible1.3及其以后的版本才支持；
- default目录：应当包含一个main.yml文件,用于为当前角色设定默认变量时使用此目录；



```
# mkdir -pv ansible_playbooks/roles/{webserver,dbserver}/{tasks,files,templates,meta,handlers,vars} 
# cp /etc/httpd/conf/httpd.conf files/  
# pwd
/root/ansible_playbooks/roles/webserver 
# cat tasks/main.yml 
- name: install httpd package
 yum: name=httpd state=present
- name: install configuretion file
 copy: src=httpd.conf dest=/etc/httpd/conf/httpd.conf
 tags:
 - conf
 notify:
 - restart httpd
- name: start httpd
 service: name=httpd state=started

# cat handlers/main.yml 
- name: restart httpd
 service: name=httpd state=restarted
   
# pwd;ls
/root/ansible_playbooks
roles  site.yml 


# cat site.yml 
- hosts: webserver
 remote_user: root
 roles:
 - webserver

# ansible-playbook site.yml
```



## Tags

tags用于让用户选择运行或跳过playbook中的部分代码。ansible具有幂等性，因此会自动跳过没有变化的部分，即便如此，有些代码为测试其确实没有发生变化的时间依然会非常的长。此时，如果确信其没有变化，就可以通过tags跳过此些代码片段。

tags：在playbook可以为某个或某些任务定义一个"标签"，在执行此playbook时，通过为ansible-playbook命令使用--tags选项能耐实现仅运行指定的tasks而非所有的；



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
   template: src=/root/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
   tags:
   - conf
   notify: 
   - restart httpd
 - name: start httpd service
   service: enabled=true name={{ service }} state=started
 
 handlers:
 - name: restart httpd
   service: name=httpd state=restarted

# ansible-playbook apache.yml --tags='conf'
```

特殊tags：always #无论如何都会运行