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





## 文件隐藏属性 

文件有隐藏属性，隐藏属性对系统有很大的帮助。尤其是在系统安全（Security）方面，非常重要。

### chattr

```
chattr [+-=][ASacdistu] 文件或目录名 
```



```
[root@linux~]# cd /tmp 
[root@linuxtmp]# touch attrtest 
[root@linuxtmp]# chattr +i attrtest 
[root@linuxtmp]# rm attrtest 
rm: remove write-protected regular empty file `attrtest'? y 
rm: cannot remove `attrtest': Operation not permitted 
```

> 看到了吗？连root也没有办法删除这个文件。赶紧解除设置。 



```
[root@linuxtmp]# chattr -i attrtest 
```

这个命令很重要，尤其是在系统的安全性方面。由于这些属性是隐藏的，所以需要用lsattr才能看到。

笔者认为，最重要的是 +i属性，因为它可以让一个文件无法被更改，对于需要很高系统安全性的人来说，相当重要。还有相当多的属性是需要root才能设置的。此外，如果是登录文件，就更需要 +a参数，使之可以增加但不能修改与删除原有的数据。将来提到登录文件时，我们再来介绍如何设置它。 

### lsattr

```
lsattr [-aR] 文件或目录 
```



```
-a : 将隐藏文件的属性也显示出来。 

-R : 连同子目录的数据也一并列出来。 
```





```
[root@linuxtmp]# chattr +aij attrtest 
[root@linuxtmp]# lsattr 
----ia---j--- ./attrtest 
```

使用chattr设置后，可以利用lsattr来查看隐藏属性。不过，这两个命令在使用上必须要特别小心，否则会造成很大的困扰。例如，某天你心情好，突然将 /etc/shadow这个重要的密码记录文件设置为具有i属性，那么，过了若干天之后，突然要新增用户，却一直无法新增。怎么办？将i的属性去掉即可。 

