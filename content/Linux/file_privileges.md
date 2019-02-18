---
title: "file_privileges"
date: 2018-12-22 16:02
---


[TOC]



# 文件权限



## 特殊权限

### Set UID

SUID对目录是无效的



会创建s与t权限，是为了让一般用户在执行某些程序的时候，能够暂时具有该程序拥有者的权限。

举例来说，我们知道，账号与密码的存放文件其实是 /etc/passwd与 /etc/shadow。而 /etc/shadow文件的权限是“-r--------”。它的拥有者是root。在这个权限中，仅有root可以“强制”存储，其他人是连看都不行的。 

但是，偏偏笔者使用dmtsai这个一般身份用户去更新自己的密码时，使用的就是 /usr/bin/passwd程序，却可以更新自己的密码。也就是说，dmtsai这个一般身份用户可以存取 /etc/shadow密码文件。这怎么可能？明明 /etc/shadow就是没有dmtsai可存取的权限。这就是因为有s权限的帮助。当s权限在user的x时，也就是类似 -r-s--x--x，称为Set UID，简称为SUID，这个UID表示User的ID，而User表示这个程序（/usr/bin/passwd）的拥有者（root）。那么，我们就可以知道，当dmtsai用户执行 /usr/bin/passwd时，它就会“暂时”得到文件拥有者root的权限。 

SUID仅可用在“二进制文件（binary file）”，SUID因为是程序在执行过程中拥有文件拥有者的权限，因此，它仅可用于二进制文件，不能用在批处理文件（shell脚本）上。这是因为shell脚本只是将很多二进制执行文件调进来执行而已。所以SUID的权限部分，还是要看shell脚本调用进来的程序设置，而不是shell脚本本身。



#### 设置SUID

假设要将一个文件属性改为“-rwsr-xr-x”，由于s在用户权限中，所以是SUID，因此，在原先的755之前还要加上4，也就是使用“chmod 4755 filename”来设置。

```
[root@linuxtmp]# chmod 4755 test; ls -l test
-rwsr-xr-x 1 root root 0 Jul 20 11:27 test
```



### Set GID

如果s的权限是在用户组，那么就是Set GID，简称为SGID

SGID可以用在两个方面。 

文件：如果SGID设置在二进制文件上，则不论用户是谁，在执行该程序的时候，它的有效用户组（effective group）将会变成该程序的用户组所有者（group id）。 

目录：如果SGID是设置在A目录上，则在该A目录内所建立的文件或目录的用户组，将会是此A目录的用户组。 

一般来说，SGID多用在特定的多人团队的项目开发上，在系统中用得较少。 



#### 设置SGID

```
[root@linuxtmp]# chmod 6755 test; ls -l test
-rwsr-sr-x 1 root root 0 Jul 20 11:27 test
```





### Set Sticky bit

这个Sticky Bit当前只针对目录有效，对文件没有效果。

SBit对目录的作用是：“在具有SBit的目录下，用户若在该目录下具有w及x权限，则当用户在该目录下建立文件或目录时，只有文件拥有者与root才有权力删除”。换句话说：当甲用户在A目录下拥有group或other的项目，且拥有w权限，这表示甲用户对该目录内任何人建立的目录或文件均可进行“删除/重命名/移动”等操作。不过，如果将A目录加上了Sticky bit的权限，则甲只能够针对自己建立的文件或目录进行删除/重命名/移动等操作。 

举例来说，/tmp本身的权限是“drwxrwxrwt”，在这样的权限内容下，任何人都可以在 /tmp内新增、修改文件，但仅有该文件/目录的建立者与root能够删除自己的目录或文件。这个特性也很重要。 



#### 设置SBIT

```
[root@linuxtmp]# chmod 1755 test; ls -l test
-rwxr-xr-t 1 root root 0 Jul 20 11:27 test
```



### 设置不含执行权限的特殊权限

```
[root@linuxtmp]# chmod 7666 test; ls -l test
-rwSrwSrwT 1 root root 0 Jul 20 11:27 test
```





