---
title: "os"
date: 2018-07-02 18:06
---

[TOC]



# OS 模块





## os.chmod

修改文件权限和时间戳



## os.chdir(dirname)

改变工作目录到dirname



## os.chown

修改文件的属主，属组，需要足够的权限Ω



## os.curdir()

返回当前目录（'.'）



## os.exit()

终止当前进程



## os.environ

查看环境变量

```
In [45]: os.environ
Out[45]: environ({'TERM_PROGRAM': 'iTerm.app', 'PYENV_ROOT': '/Users/xhxu/.pyenv', 'NVM_CD_FLAGS': '', 'SHELL': '/bin/bash', 'TERM': 'xterm-256color', 'TMPDIR': '/var/folders/rz/7zd4wvf90070rc96bxfc_xf13wzxml/T/', 'Apple_PubSub_Socket_Render': '/private/tmp/com.apple.launchd.iV428QMhhK/Render', 'TERM_PROGRAM_VERSION': '3.0.14', 'TERM_SESSION_ID': 'w0t2p0:1094E04E-FF61-4FC1-A33E-6C31039D1431', 'SDKMAN_PLATFORM': 'Darwin', 'PYENV_VERSION': 'env3.6.3_simiki', 'SDKMAN_CURRENT_API': 'https://api.sdkman.io/2', 'USER': 'xhxu', 'NVM_DIR': '/Users/xhxu/.nvm', 'SDKMAN_LEGACY_API': 'https://api.sdkman.io/1', 'SSH_AUTH_SOCK': '/private/tmp/com.apple.launchd.huXZtZvXV6/Listeners', 'PYENV_DIR': '/Users/xhxu/simiki', '__CF_USER_TEXT_ENCODING': '0x47CFF693:0x0:0x0', 'PYENV_VIRTUALENV_INIT': '1', 'VIRTUAL_ENV': '/Users/xhxu/.pyenv/versions/3.6.3/envs/env3.6.3_simiki', 'PYENV_VIRTUAL_ENV': '/Users/xhxu/.pyenv/versions/3.6.3/envs/env3.6.3_simiki', 'PATH': '/Users/xhxu/.pyenv/versions/env3.6.3_simiki/bin:/usr/local/Cellar/pyenv/1.2.4/libexec:/Users/xhxu/.sdkman/candidates/groovy/current/bin:/Users/xhxu/apache-maven-3.5.3/bin:/Users/xhxu/.jenv/shims:/Users/xhxu/.jenv/bin:/Users/xhxu/.nvm/versions/node/v7.6.0/bin:/usr/local/Cellar/pyenv-virtualenv/1.0.0/shims:/Users/xhxu/.pyenv/shims:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Wireshark.app/Contents/MacOS', 'PWD': '/Users/xhxu/simiki', 'JENV_LOADED': '1', 'LANG': 'en_US.UTF-8', 'ITERM_PROFILE': 'Default', 'SDKMAN_VERSION': '5.6.3+299', 'PYENV_HOOK_PATH': '/Users/xhxu/.pyenv/pyenv.d:/usr/local/Cellar/pyenv/1.2.4/pyenv.d:/usr/local/etc/pyenv.d:/etc/pyenv.d:/usr/lib/pyenv/hooks', 'XPC_FLAGS': '0x0', '_OLD_VIRTUAL_PS1': '\\h:\\W \\u\\$ ', 'XPC_SERVICE_NAME': '0', 'COLORFGBG': '7;0', 'HOME': '/Users/xhxu', 'SHLVL': '1', 'PYENV_SHELL': 'bash', 'ITERM_SESSION_ID': 'w0t2p0:1094E04E-FF61-4FC1-A33E-6C31039D1431', 'LOGNAME': 'xhxu', 'SDKMAN_DIR': '/Users/xhxu/.sdkman', 'GROOVY_HOME': '/Users/xhxu/.sdkman/candidates/groovy/current', 'NVM_BIN': '/Users/xhxu/.nvm/versions/node/v7.6.0/bin', 'SDKMAN_CANDIDATES_DIR': '/Users/xhxu/.sdkman/candidates', 'SECURITYSESSIONID': '186a7'})
```



* 获取当前用户的主目录路径（家目录）

```
In [8]: os.environ['HOME']
Out[8]: '/Users/xhxu'
```





## os.getcwd

得到当前工作目录，即当前python脚本工作的目录路径。

## os.getenv()和os.putenv

分别用来读取和设置环境变量


## os.getatime(path)

返回path所指向的尾巴尖或者目录的最后存取时间



## os.getmtime(path)

返回path所指向的尾巴尖或者目录的最后修改时间



## os.listdir()

返回指定目录下的所有文件和目录名

```
In [24]: os.listdir()
Out[24]:
['.DS_Store',
 'Snapshots']
```



## os.linesep

给出当前平台的行终止符。例如，Windows使用'\r\n'，Linux使用'\n'而Mac使用'\r'

```
In [46]: os.linesep
Out[46]: '\n'
```



## os.mkdir

创建目录

```
import os
os.mkdir('mydir')
```



## os.makedirs

创建多级目录

```
os.makedirs(r'a/b/c')
```





## os.name
指示你正在使用的工作平台。比如对于Windows，它是'nt'，而对于Linux/Unix用户，它是'posix'。

```
In [47]: os.name
Out[47]: 'posix'
```



## os.path.abspath(name)

获得文件的绝对路径

```
os.path.abspath(__file__)
```



## os.path.basename(path)

返回文件名

```
In [2]: os.path.basename('_config.yml')
Out[2]: '_config.yml'
```

```
In [15]: p
Out[15]: '/etc/sysconfig/network'

In [19]: path.basename(p)
Out[19]: 'network'
```



## os.path.exists(name)

判断是否存在文件或目录name

```
In [13]: from os import path

In [14]: p = path.join('/etc', 'sysconfig', 'network')

In [15]: p
Out[15]: '/etc/sysconfig/network'

In [16]: path.exists(p)
Out[16]: False

In [17]: path.split(p)
Out[17]: ('/etc/sysconfig', 'network')
```



## os.path.expandvars()

获取当前用户的主目录路径（家目录）

```
In [10]: os.path.expandvars('$HOME')
Out[10]: '/Users/xhxu'
```



## os.path.expanduser()

获取当前用户的主目录路径（家目录）

```
In [11]: os.path.expanduser('~')
Out[11]: '/Users/xhxu'
```



## os.path.isfile()
分别检验给出的路径是一个目录还是文件

## os.path.isdir()
分别检验给出的路径是一个目录还是文件

## os.path.isabs()

判断是否为绝对路径



## os.path.isdir(name)
判断name是不是目录，不是目录就返回false

## os.path.isfile(name)
判断name这个文件是否存在，不存在返回false



## os.path.getsize(name)
或得文件大小，如果name是目录返回0L



## os.path.join(path,name)

连接目录与文件名或目录

```
In [13]: from os import path

In [14]: p = path.join('/etc', 'sysconfig', 'network')

In [15]: p
Out[15]: '/etc/sysconfig/network'

In [16]: path.exists(p)
Out[16]: False

In [17]: path.split(p)
Out[17]: ('/etc/sysconfig', 'network')
```



## os.path.normpath(path)
规范path字符串形式

## os.path.split()
分割文件名与目录（事实上，如果你完全使用目录，它也会将最后一个目录作为文件名而分离，同时它不会判断文件或目录是否存在）

```
In [13]: from os import path

In [14]: p = path.join('/etc', 'sysconfig', 'network')

In [15]: p
Out[15]: '/etc/sysconfig/network'

In [16]: path.exists(p)
Out[16]: False

In [17]: path.split(p)
Out[17]: ('/etc/sysconfig', 'network')
```



## os.path.splitext()
分离文件名和扩展名





## os.path.dirname(path)
返回当前文件路径

```
os.path.dirname(__file__)
```

返回父目录文件路径

```
os.path.dirname(os.path.dirname(__file__))
```



```
In [15]: p
Out[15]: '/etc/sysconfig/network'

In [20]: path.dirname(p)
Out[20]: '/etc/sysconfig'
```



## os.pathsep

文件路径分隔符

```
In [43]: os.pathsep
Out[43]: ':'
```



## os.popen

可以保存执行结果，必须通过read方法处理

```
import os
cmd_result = os.open('ls').read()
print(cmd_result)
```



## os.remove(file)

删除一个文件



## os.removedirs()

删除多级目录

```
os.removedirs(r'a/b/c')
```

## os.rmdir(name)

删除目录

## os.removedirs()

删除多个目录

```
os.removedirs(r'a/b/c')
```



## os.sep

取代操作系统特定的路径分隔符

```
In [41]: os.sep
Out[41]: '/'
```





## os.stat() 文件属性

获得文件属性

```
In [29]: os.stat('1.py')
Out[29]: os.stat_result(st_mode=33279, st_ino=6332744, st_dev=16777220, st_nlink=1, st_uid=1204811411, st_gid=254449427, st_size=910, st_atime=1548951081, st_mtime=1511237678, st_ctime=1543894951)
```



## os.system (deprecate)

执行命令，不保存结果

```
import os
print(os.system('ls'))   
```



## os.symlink(source, link_name)

为源文件source创建一个符号链接link_name。



## os.uname()

```
In [23]: os.uname()
Out[23]: posix.uname_result(sysname='Darwin', nodename='mac', release='17.7.0', version='Darwin Kernel Version 17.7.0: Fri Jul  6 19:54:51 PDT 2018; root:xnu-4570.71.3~2/RELEASE_X86_64', machine='x86_64')
```

 

## os.unlink(path)

删除一个path指定的文件，和os.remove()的工作机制一样。

 

## os.utime(path, times)

修改文件的访问时间和修改时间。如果times参数为**None**，则设置文件的访问时间和修改时间为当前的时间。否则，如果times参数不为空，则times参数是一个二元组(atime, mtime)，用于设置文件的访问时间和修改时间。

 

## os.tempnam([dir[, prefix]])

返回一个独一无二的路径名作为临时文件的文件名。返回的路径名是一个绝对路径，该临时文件名的入口由dir参数指定，如果dir参数没有指定或者为**None**，则用通用的临时文件目录作为临时文件的目录。如果dir被指定，并且不是**None**，则prefix参数被用来作为创建的临时文件名的前缀。

 

## os.tmpnam()

返回一个独一无二的路径名作为临时文件的文件名，该文件名被创建者通用的临时文件目录下。

>  os.tmpnam()和os.tempnam()只是负责生产一个临时文件的路径名，而不负责文件的创建和删除。

 

## os.walk()

os.walk(top, topdown = True, onerror = None, followlinks = False)

以自顶向下遍历目录树或者以自底向上遍历目录树，对每一个目录都返回一个三元组(dirpath, dirnames, filenames)。

三元组(dirpath，dirnames，filenames)：

dirpath - 遍历所在目录树的位置，是一个字符串对象

dirnames - 目录树中的子目录组成的列表，不包括("."和"..")

filenames - 目录树中的文件组成的列表

如果可选参数topdown = True或者没有指定，则其实目录的三元组先于其子目录的三元组生成(自顶向下生成三元组)，如果topdown = False，则起始目录的三元组在其子目录的三元组生成后才生成(自底向上生成三元组)。

当topdown = True，os.walk()函数会就地修改三元组中的*dirnames*列表(可能是使用del或者进行切片），然后再使用os.walk()递归地处理剩余在*dirnames*列表中的目录。这种方式有助于加快搜索效率，可以指定特殊的遍历顺序。当topdown = False的时候修改*dirnames*是无效的，因为在使用自底向上进行遍历的时候子目录的三元组是先于上一级目录的三元组创建的。

默认情况下，调用listdir()返回的错误会被忽略，如果可选参数oneerror被指定，则oneerror必须是一个函数，该函数有一个OSError实例的参数，这样可以允许在运行的时候即使出现错误的时候不会打断os.walk()的执行，或者抛出一个异常并终止os.walk()的运行。
默认情况下，os.walk()遍历的时候不会进入符号链接，如果设置了可选参数followlinks = True，则可以进入符号链接。

注意：当设置followlinks = True时，可能会出现循环遍历，因为符号链接可能会出现自己链接自己的情况，而os.walk()不会意识到这一点。

注意：如果传递过去的路径名是一个相对路径，则不会修改当前的工作路径。

使用os.walk遍历目录真的很方便：

```
#-*- coding:utf-8 -*-

import os

if __name__ == '__main__':
    try:
    '''traval and list all files and all dirs''' 
    for root, dirs, files in os.walk('D:' + os.sep + 'Python27'):
        print '-------------------directory < ' + root + ' > --------------------------'

        for d in dirs:
        print d
        for f in files:
        print f
    except OSError, e:
    print os.strerror(e.errno)
```





# 文件操作

`fp = open("text.txt",w)`:直接打开一个文件，如果文件不存在就创建文件



## `open`的模式

```
w 写方式
a 追加模式打开（从EOF开始，必要时创建新文件）
r+ 以读写模式打开
w+ 以读写模式打开
a+ 以读写模式打开
rb 以二进制读模式打开
wb 以二进制写模式打开 (参见 w )
ab 以二进制追加模式打开 (参见 a )
rb+ 以二进制读写模式打开 (参见 r+ )
wb+ 以二进制读写模式打开 (参见 w+ )
ab+ 以二进制读写模式打开 (参见 a+ )
```



## 文件的函数

### fp.read([size])                    
size为读取的长度，以byte为单位

### fp.readline([size])                
读一行，如果定义了size，有可能返回的只是一行的一部分

### fp.readlines([size])               
把文件每一行作为一个list的一个成员，并返回这个list。其实它的内部是通过循环调用readline()来实现的。如果提供size参数，size是表示读取内容的总长，也就是说可能只读到文件的一部分。

### fp.write(str)                      
把str写到文件中，write()并不会在str后加上一个换行符



### fp.writelines(seq)                  
把seq的内容全部写到文件中(多行一次性写入)。这个函数也只是忠实地写入，不会在每行后面加上任何东西。



### fp.close()                        
关闭文件。python会在一个文件不用后自动关闭文件，不过这一功能没有保证，最好还是养成自己关闭的习惯。 如果一个文件在关闭后还对其进行操作会产生ValueError

### fp.flush()                                      
把缓冲区的内容写入硬盘

### fp.fileno()                                      
返回一个长整型的”文件标签“

### fp.isatty()                                      
文件是否是一个终端设备文件（unix系统中的）

### fp.tell()                                         
返回文件操作标记的当前位置，以文件的开头为原点

### fp.next()                                       
返回下一行，并将文件操作标记位移到下一行。把一个file用于for … in file这样的语句时，就是调用next()函数来实现遍历的。

### fp.seek(offset[,whence])              
将文件打操作标记移到offset的位置。这个offset一般是相对于文件的开头来计算的，一般为正数。但如果提供了whence参数就不一定了，whence可以为0表示从头开始计算，1表示以当前位置为原点计算。2表示以文件末尾为原点进行计算。需要注意，如果文件以a或a+的模式打开，每次进行写操作时，文件操作标记会自动返回到文件末尾。

### fp.truncate([size])                       
把文件裁成规定的大小，默认的是裁到当前文件操作标记的位置。如果size比文件的大小还要大，依据系统的不同可能是不改变文件，也可能是用0把文件补到相应的大小，也可能是以一些随机的内容加上去。





# 目录操作


## 复制文件

shutil.copyfile("oldfile","newfile")      

oldfile和newfile都只能是文件

shutil.copy("oldfile","newfile")            

oldfile只能是文件夹，newfile可以是文件，也可以是目标目录

shutil.copytree("olddir","newdir")       

复制文件夹.olddir和newdir都只能是目录，且newdir必须不存在

os.rename("oldname","newname")       

重命名文件（目录）.文件或目录都是使用这条命令

shutil.move("oldpos","newpos")   

移动文件（目录）

os.rmdir("dir")
只能删除空目录

shutil.rmtree("dir")    

空目录、有内容的目录都可以删