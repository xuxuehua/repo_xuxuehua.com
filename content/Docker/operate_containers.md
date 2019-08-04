---
title: "operate_containers"
date: 2018-10-24 16:23
---


[TOC]


# operate_containers

在 Docker 1.13+ 推荐使用 docker container 子命令来管理 Docker 容器。



## create 创建容器

```
docker create
```





## run 运行容器

docker run 就是运行容器的命令

```
docker run ubuntu:14.04 /bin/echo 'Hello World!'
```



### -t 伪终端

让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上



### -i 标准输入

让容器的标准输入保持打开

```
docker run -t -i ubuntu:14.04 /bin/bash
```



### -d 后台运行

让 Docker 在后台运行可以通过添加 -d 参数来实现。

> 此时容器会在后台运行并不会把输出的结果 (STDOUT) 打印到宿主机上面(输出结果可以用 docker logs 查

```
$ docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

> 容器是否会长久运行，是和 docker run 指定的命令有关



### -v volumn 

进程在容器对被挂载目录（如test）进行的所有操作，都实际发生在宿主机的对应目录，（如home，或者/var/lib/docker/volumes/[VOLUME_ID]/_data，里面不会影响容器容器镜像

其本质，是将/test目录的入口，重定向到/home目录的inode上，实际修改的是/home目录的inode



默认容器对这个目录有可读写权限，可以通过指定ro，将权限改为只读（readonly）

```
docker run --name some-nginx -p 80:80 -v /some/content:/usr/share/nginx/html:ro -d nginx
```





```
docker run -v /test 
```

> 由于没有显式指定宿主机目录，默认在宿主机上创建临时目录
>
> /var/lib/docker/volumes/[VOLUME_ID]/_data 



```
docker run -v /home:/test
```

> 将宿主机目录/home 挂载进容器的/test 目录当中









### --rm 容器退出后删除

这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。



### --restart

自动重启

always 总是自动重启



## volume 查看卷



### ls 查看卷ID

```
# docker run -d -v /test helloworld
40a9cbd61d78451c74be1034ddaa1079e3e687ba4f7b557aff96f15bea5303cb

# docker volume ls
DRIVER              VOLUME NAME
local               c0dbc7dc970abb3ab332cf956b823e60286e4be5ecaa38faa2e7bf04a3d53677
```



可以查看到该目录

```
# ls -lah /var/lib/docker/volumes/c0dbc7dc970abb3ab332cf956b823e60286e4be5ecaa38faa2e7bf04a3d53677/_data/
total 8.0K
drwxr-xr-x 2 root root 4.0K Mar  9 16:54 .
drwxr-xr-x 3 root root 4.0K Mar  9 16:54 ..
```



进入容器创建文件，可以看到文本被创建了

```
# docker exec -it loving_davinci /bin/bash
root@40a9cbd61d78:/test# touch text.txt
root@40a9cbd61d78:/test# exit
root@119:~# ls
1  1.sh  Dockerfile  app.py  cc  requirements.txt
root@119:~# ls -lah /var/lib/docker/volumes/c0dbc7dc970abb3ab332cf956b823e60286e4be5ecaa38faa2e7bf04a3d53677/_data/
total 8.0K
drwxr-xr-x 2 root root 4.0K Mar  9 16:59 .
drwxr-xr-x 3 root root 4.0K Mar  9 16:54 ..
-rw-r--r-- 1 root root    0 Mar  9 16:59 text.txt
```



## start 启动容器

```
docker start
```



## stop 终止容器

docker stop container_id 来终止一个运行中的容器。



Stop all running containers

```
docker stop $(docker ps -aq)
```

## restart 重启容器

docker restart container_id 命令会将一个运行态的容器终止，然后再重新启动它



## inspect 查看容器

```
docker inspect --format '{{ .State.Pid }}' af3f3c9d2428
```



## logs 查看容器日志

```
docker logs
```



## kill 杀死运行中的容器

```
docker kill
```







## 进入容器

* 使用 docker attach 命令或 nsenter 工具等。

### attach 命令

* docker attach 是 Docker 自带的命令
> 但是使用 attach 命令有时候并不方便。当多个窗口同时 attach 到同一个容器的时候，所有窗口都会同步显示。当某个窗口因命令阻塞时,其他窗口也无法执行操作了。
```
$ docker run -idt ubuntu
243c32535da7d142fb0e6df616a3c3ada0b8ab417937c853a9e1c251f499f550
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
243c32535da7        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           nostalgic_hypatia
$ docker attach nostalgic_hypatia
root@243c32535da7:/#
```

### nsenter 命令

* nsenter 工具在 util-linux 包2.23版本后包含

#### 安装 nsenter 

```
cd /tmp && wget wget https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz && tar -xf util-linux-2.24.tar.gz && cd util-linux-2.24 && ./configure --without-ncurses && make nsenter && sudo cp nsenter /usr/local/bin  



curl https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz | tar -zxf-; cd util-linux-2.24;
$ ./configure --without-ncurses
$ make nsenter && sudo cp nsenter /usr/local/bin
```

#### 使用 nsenter 

* nsenter 启动一个新的shell进程(默认是/bin/bash), 同时会把这个新进程切换到和目标(target)进程相同的命名空间，这样就相当于进入了容器内部。

* 为了连接到容器，你还需要找到容器的第一个进程的 PID，可以通过下面的命令获取。

```
PID=$(docker inspect --format "{{ .State.Pid }}" <container>)
```

* 通过这个 PID，就可以连接到这个容器：

```
$ nsenter --target $PID --mount --uts --ipc --net --pid
```

* 如果无法通过以上命令连接到这个容器，有可能是因为宿主的默认shell在容器中并不存在，比如zsh，可以使用如下命令显式地使用bash。

```
$ nsenter --target $pid --mount --uts --ipc --net --pid  -- /usr/bin/env \
--ignore-environment HOME=/root /bin/bash --login
```



#### 完整使用nsenter的例子

```
$ docker run -idt ubuntu
243c32535da7d142fb0e6df616a3c3ada0b8ab417937c853a9e1c251f499f550
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
243c32535da7        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           nostalgic_hypatia
$ (docker inspect --format "{{ .State.Pid }}" $(docker ps  -q))
10981
$ sudo nsenter --target 10981 --mount --uts --ipc --net --pid
root@243c32535da7:/#
```



### exec 命令

一个进程，可以选择加入到某个进程已有的Namespace当中，从而达到进入这个进程所在的容器，这就是docker exec的实现原理

```
$ sudo docker ps  
$ sudo docker exec -it 775c7c9ee1e1 /bin/bash  
```





## export 导出容器

使用 docker export 命令

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
7691a814370e        ubuntu:14.04        "/bin/bash"         36 hours ago        Exited (0) 21 hours ago                       test
$ docker export 7691a814370e > ubuntu.tar
```



## import 导入容器

使用 docker import 从容器快照文件中再导入为镜像

```
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```

通过指定 URL 或者某个目录来导入

```
$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

> 注：用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以使用 docker import 来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。



## rm 删除容器

docker rm 来删除一个处于终止状态的容器

```
$ docker rm  trusting_newton
trusting_newton
```



### -f 删除运行中的容器



## prune 清理容器

用 docker container prune 可以清理掉所有处于终止状态的容器。



## ps 查看容器

用 docker ps -a 命令可以查看所有已经创建的包括终止状态的容器