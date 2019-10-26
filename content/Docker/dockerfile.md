---
title: "dockerfile"
date: 2018-10-24 16:12
---


[TOC]

# dockerfile

纯文本文件，通过其命令构建一个镜像





## FROM

在Dockerfile中第一条非注释INSTRUCTION一定是`FROM`，它决定了以哪一个镜像作为基准，`<image>`首选本地是否存在，如果不存在则会从公共仓库下载（当然也可以使用私有仓库的格式）

```
FROM  <image>
或
FROM <image>:<tag>
```




The same pattern you use in `docker push` works:

```
FROM 1234567890.dkr.ecr.us-west-2.amazonaws.com/mycompany:latest
```



## RUN 指令安装

在docker build 期间的构建指令，RUN可以运行任何被基础image支持的命令。如基础image选择了ubuntu，那么软件管理部分只能使用ubuntu的命令。

- RUN命令将在当前image中执行任意合法命令并提交执行结果。命令执行提交后，就会自动执行Dockerfile中的下一个指令。
- 层级 RUN 指令和生成提交是符合Docker核心理念的做法。它允许像版本控制那样，在任意一个点，对image 镜像进行定制化构建。
- RUN 指令缓存不会在下个命令执行时自动失效。比如 RUN apt-get dist-upgrade -y 的缓存就可能被用于下一个指令. --no-cache 标志可以被用于强制取消缓存使用。



`CMD`与`RUN`的区别在于，`RUN`是在`build`成镜像时就运行的，先于`CMD`和`ENTRYPOINT`的，`CMD`会在每次启动容器的时候运行，而`RUN`只在创建镜像时执行一次，固化在image中。

```
RUN <commnad>
或
RUN ["executable", "param1", "param2"]
```



### shell 格式

shell格式，相当于执行`/bin/sh -c "<command>"` 这样意味着此进程在容器中的PID不为1，不能接收Unix信号， 当使用docker stop <container_id> 命令停止容器时，此进程接收不到SIGTERM信号

```
RUN apt-get install vim -y
```



### exec 格式

exec格式，不会触发以/bin/sh -c来发起的操作，常见的shell 操作如变量替换以及通配符等替换不会执行，所以`$HOME`这样的环境变量无法使用，不过可以在内部显式替换以生效

```
RUN ["apt-get", "install", "vim", "-y"]
或
RUN ["/bin/bash", "-c", "apt-get install vim -y"]  与shell风格相同
```





## COPY 复制文件

复制本地主机的当前的src文件 到container的dest

```
COPY <src>... <desk>
COPY ["<源路径1>",... "<目标路径>"]
```

src 必须是build上下文中的路径，不能是其父目录中的文件， Dockerfile所在的目录

如果src是目录，其内部文件或子目录会被递归，但src目录自身不会被复制 

```
COPY yum.repos.d /etc/yum.repos.d/
```

如果指定多个src，或在src中使用通配符，dest必须是同一个目录，必须以/结尾

如果dest不存在，会被自动创建，包括父目录路径



Same as ‘ADD’ but without the tar and remote url handling.

`COPY`的语法与功能与`ADD`相同，只是不支持上面讲到的`<src>`是远程URL、自动解压这两个特性，但是[Best Practices for Writing Dockerfiles](https://docs.docker.com/articles/dockerfile_best-practices/)建议**尽量使用COPY**，并使用`RUN`与`COPY`的组合来代替`ADD`，这是因为虽然`COPY`只支持本地文件拷贝到container，但它的处理比`ADD`更加透明，建议只在复制tar文件时使用`ADD`，如`ADD trusty-core-amd64.tar.gz /`。



### 指定权限复制

**For versions v17.09.0-ce and newer**

Use the optional flag `--chown=<user>:<group>` with either the `ADD` or `COPY` commands.

For example

```

```





## ADD 

```
ADD <src>... <dest>
```

将文件`<src>`拷贝到container的文件系统对应的路径`<dest>`下。

ADD 支持TAR文件和URL路径

操作同COPY指令

如果src为URL且dest不以/结尾，则src指定的文件将被下载并直接被创建为dest

如果dest以/结尾，则文件名URL指定的文件将被直接下载并保存为dest/filename

如果src是一个本地系统上说他人文件，将被展开为一个目录，其行为类似于tar -x，然而通过URL获取的tar文件不会自动展开

如果src有多个，或间接直接使用了通配符，则dest必须是一个以/结尾的目录路径，

如果dest不以/结尾，则被视为一个普通文件，src的内容将被直接写入到dest



构建指令，所有拷贝到container中的文件和文件夹权限为0755，uid和gid为0；如果是一个目录，那么会将该目录下的所有文件添加到container中，不包括目录



```
支持模糊匹配
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character

ADD requirements.txt /tmp/
RUN pip install /tmp/requirements.txt
ADD . /tmp/
```

另外`ADD`支持远程URL获取文件，但官方认为是`strongly discouraged`，建议使用`wget`或`curl`代替。
`ADD`还支持自动解压tar文件，比如`ADD trusty-core-amd64.tar.gz /`会线自动解压内容再COPY到在容器的`/`目录下。

ADD只有在build镜像的时候运行一次，后面运行container的时候不会再重新加载，也就是你不能在运行时通过这种方式向容器中传送文件，`-v`选项映射本地到容器的目录。





## MAINTAINER 维护者 （deprecated ）

```
MAINTAINER <name>
```

构建指令，用于将image的制作者相关的信息写入到image中。当我们对该image执行docker inspect命令时，输出中有相应的字段记录该信息。









## CMD 容器启动命令

用于container启动时指定的操作， 即docker run期间的操作。该操作可以是执行自定义脚本，也可以是执行系统命令。该指令只能在文件中存在一次，如果有多个，则只执行最后一条。



CMD 可在 Dockerfile 中配置，在启动容器时会被  `docker run <image>` 后的参数覆盖

CMD 的 exec 格式中，第一个元素是 shell 的 `$0`, 其余元素是 shell 的 `$@`。当 ENTRYPOINT 中用 shell 格式或显式的 sh(bash等)就可以引用 `$0`, `$@`



### shell 格式

shell格式，相当于执行`/bin/sh -c "<command>"` 这样意味着此进程在容器中的PID不为1，不能接收Unix信号， 当使用docker stop <container_id> 命令停止容器时，此进程接收不到SIGTERM信号

```
CMD command param1 param2  (shell格式)
```



### exec 格式 （推荐）

推荐使用 exec 格式，这类格式在解析时会被解析为 JSON 数组

```
CMD ["executable","param1","param2"]  （数组/exec格式）
CMD ["param1","param2"]  (as default parameters to ENTRYPOINT)
```



```
Dockerfile:
    CMD ["echo CMD_args"]
运行
    docker run <image> echo run_arg
结果
    输出 run_arg
```

>  因为`echo run_arg`覆盖了`CMD`。如果`run`后没有`echo run_arg`，则输出`CMD_args`。





#### 参数列表格式

在指定了 ENTRYPOINT 指令后，用 CMD 指定具体的参数。

`CMD ["参数1", "参数2"...]`



## ENTRYPOINT 入口点

ENTRYPOINT 的目的和 CMD 一样，都是在指定容器启动程序及参数。

与CMD不同的是，ENTRYPOINT启动的程序不会被docker run命令行指定的参数所覆盖，而且这些命令行参数会被当作参数传递给ENTRYPOINT指定的程序

当然可以在`docker run`时使用`--entrypoint`来覆盖`ENTRYPOINT`指令。



默认ENTRYPOINT 启动的就是`/bin/sh -c` ，可以强制显式指定

```
ENTRYPOINT ["/bin/sh", "-c"]
```



### exec 格式

ENTRYPOINT 和 CMD 合并前需转换为 exec 格式(用 `docker inspect <image>` 查看)，合并后(相当于数组) 第一个元素是命令，其他都为参数



```
Dockerfile:
    ENTRYPOINT ["echo", "ENTRYPOINT_args"]
运行
    docker run <image> run_arg
结果
    输出 ENTRYPOINT_args run_arg
```

> 因为`echo run_arg`追加到`ENTRYPOIINT`的`echo`后面了。如果在`ENTRYPOINT`后再加入一行`CMD ["CMD_args"]`，则结果依旧，除非去掉run后的所有参数。
> 当出现`ENTRYPOINT`指令时`CMD`指令只可能(当`ENTRYPOINT`指令使用exec方式执行时)被当做`ENTRYPOINT`指令的参数使用，其他情况则会被忽略。





### docker run 操作

定义了 ENTRYPOINT, CMD 由 docker run 提供

```
ENTRYPOINT  ["echo", "hello"]
```

> 执行命令 docker run <image> rm -rf /, 实际入口是由 ["echo", "hello"] 与 ["rm", "-rf", "/"] 拼接而成的 ["echo", "hello", "rm", "-rf", "/"], 输出为 hello rm -rf /

ENTRYPOINT 同样可以被覆盖，如 docker run --entrypoint ls test -l /，将会执行 ls -l / 命令。





#### shell 格式

使用shell格式，`ENTRYPOINT`相当于执行`/bin/sh -c <command..>`，这种格式会忽略`docker run`和`CMD`的所有参数。

另一方面 CMD 或 `docker run <image>` 的输入第一个元素存成了 `$0`，其他剩余元素存为 `$@`, 所以 shell 格式的 ENTRYPOINT 可以这么写

```
ENTRYPOINT echo hello $0 $@
```

注：shell 中 `$0` 表示命令本身，`$@` 为所有参数

这样执行下面 docker 命令将可获得所有的参数输入

```
$ docker run test world and China
hello world and China
```

如果只是按常规 shell 脚本来对待，想当然的写成

```
ENTRYPOINT echo hello $@
```

效果将是

```
$ docker run test world and China
hello and China
```

第一个参数将被丢失，`docker run <image>` 后第一个输入通常是一个命令，所以是 `$0`, 而 ENTRYPOINT 又希望它是一个普通参数，因此`$0 $@` 要同时写上。



#### shopt -s nullglob

在使用 Linux 中的通配符时 `* ?等` 如果没有匹配到任何文件, 不会报 `No such file or directory` 而是将命令后面的参数去掉执行



#### if [ “${1:0:1}” = ‘-‘ ]; then…

这是一个判断语句, 在官方文件中, 上一行已经给出了注释: `if command starts with an option, prepend mysqld`

这个判断语句是 `${1:0:1}` 意思是判断 $1(调用该脚本的第一个参数), 偏移量0(不偏移), 取一个字符(取字符串的长度)

如果判断出来调用这个脚本后面所跟的参数第一个字符是`-`中横线的话, 就认为后面的所有字符串都是 mysqld 的启动参数

上面的这个操作类似于 Python 的字符串切片





#### set – mysqld “$@”

在上面判断完第一个参数是`-`开头之后, 紧接着就执行了 `set -- mysqld "$@"` 这个命令. 使用了 `set --` 的用法. `set —`会将他后面所有以空格区分的字符串, 按顺序分别存储到$1, $2, $3 变量中, 其中新的`$@` 为 `set —` 后面的全部内容

举例来说: `bash docker-entrypoint.sh -f xxx.conf`

在这种情况下, `set -- mysqld "$@"` 中的 `$@` 的值为 `-f xxx.conf`

当执行完 `set -- mysqld "$@"` 这条命令后:

- `$1=mysqld`
- `$2=-f`
- `$3=xxx.conf`
- `$@=mysqld -f xxx.conf`

可以看到, 当执行 `docker-entrypoint.sh`脚本的时候后面加了 `-x`形式的参数之后, $@的值发生的改变, 在原有$@值的基础之上, 在前面又预添加了 mysqld 命令

#### exec “$@”

几乎在每个 `docker-entrypoint.sh` 脚本的最后一行, 执行的都是 `exec "$@"`命令

这个命令的意义在于你已经为你的镜像预想到了应该有的调用情况, 当实际使用镜像的人执行了你没有预料到的可执行命令时, 将会走到脚本的这最后一行, 去执行用户新的可执行命令

#### 情况判断

上面直接说了脚本的最后一行, 在之前的脚本中, 需要充分的去考虑你自己的脚本可能会被调用的情况. 还是拿 MySQL 官方的 dockerfile 来说, 他判断以下情况:

- 开头是 `-` , 认为是参数的情况
- 开头是 `mysqld`, 且用户 id 为0 (root 用户) 的情况
- 开头是 `mysqld` 的情况

判断完自己应用的所有调用形态之后, 最后应该加上`exec "$@"` 命令兜底

#### ${mysql[@]}

Shell 中的数组, 直接执行 `${mysql[@]}` 会把这个数组当做可执行程序来执行

```
➜  /tmp mysql=( mysql --protocol=socket -uroot -hlocalhost --socket="${SOCKET}" )
➜  /tmp echo ${mysql[1]}
mysql
➜  /tmp echo ${mysql[2]}
--protocol=socket
➜  /tmp echo ${mysql[3]}
-uroot
➜  /tmp echo ${mysql[4]}
-hlocalhost
➜  /tmp echo ${mysql[@]}
mysql --protocol=socket -uroot -hlocalhost --socket=
```

#### exec gosu mysql `“$BASH_SOURCE” “$@”`

这里的 gosu 命令, 是 Linux 中 sudo 命令的轻量级”替代品”

gosu 是一个 golang 语言开发的工具, 用来取代 shell 中的 sudo 命令. su 和 sudo 命令有一些缺陷, 主要是会引起不确定的 TTY, 对信号量的转发也存在问题. 如果仅仅为了使用特定的用户运行程序, 使用 su 或 sudo 显得太重了, 为此 gosu 应运而生.

gosu 直接借用了 libcontainer 在容器中启动应用程序的原理, 使用 `/etc/passwd` 处理应用程序. gosu 首先找出指定的用户或用户组, 然后切换到该用户或用户组. 接下来, 使用 exec 启动应用程序. 到此为止, gosu 完成了它的工作, 不会参与到应用程序后面的声明周期中. 使用这种方式避免了 gosu 处理 TTY 和转发信号量的问题, 把这两个工作直接交给了应用程序去完成







## ENV 设置环境变量

ENV设置的环境变量，可以使用docker inspect命令来查看。同时还可以使用`docker run --env <key>=<value>`来修改环境变量。

用于其后的其他指令所调用，如ENV ADD COPY等



```
ENV <key> <value>
```

设置了后，后续的RUN命令都可以使用，当运行生成的镜像时这些环境变量依然有效，如果需要在运行时更改这些环境变量可以在运行`docker run`时添加`--env <key>=<value>`参数来修改。



```
$ docker run -e MYVAR1 --env MYVAR2=foo --env-file ./env.list ubuntu bash
```

Use the `-e`, `--env`, and `--env-file` flags to set simple (non-array) environment variables in the container you’re running, or overwrite variables that are defined in the Dockerfile of the image you’re running.

You can define the variable and its value when running the container:

```
$ docker run --env VAR1=value1 --env VAR2=value2 ubuntu env | grep VAR
VAR1=value1
VAR2=value2
```

You can also use variables that you’ve exported to your local environment:

```
export VAR1=value1
export VAR2=value2

$ docker run --env VAR1 --env VAR2 ubuntu env | grep VAR
VAR1=value1
VAR2=value2
```

When running the command, the Docker CLI client checks the value the variable has in your local environment and passes it to the container. If no `=` is provided and that variable is not exported in your local environment, the variable won’t be set in the container.

You can also load the environment variables from a file. This file should use the syntax `<variable>=value` (which sets the variable to the given value) or `<variable>` (which takes the value from the local environment), and `#` for comments.

```
$ cat env.list
# This is a comment
VAR1=value1
VAR2=value2
USER

$ docker run --env-file env.list ubuntu env | grep VAR
VAR1=value1
VAR2=value2
USER=denis
```



### 变量替换

```
${variable:-word} 变量设置，即为word，变量未设置，结果为word
${variable:+word} 变量设置，即为word，变量未设置，结果为空
```

```
rxus-MacBook-Pro:~ rxu$ echo ${NAME:-rick}
rick
rxus-MacBook-Pro:~ rxu$ NAME=Rick
rxus-MacBook-Pro:~ rxu$ echo ${NAME:-rick}
Rick
rxus-MacBook-Pro:~ rxu$ echo ${NAME:+rick}
rick
rxus-MacBook-Pro:~ rxu$ unset NAME
rxus-MacBook-Pro:~ rxu$ echo ${NAME:+rick}

```



## ARG 构建参数

ARG 定义的变量只在建立 image 时有效，建立完成后变量就失效消失

```
ARG <参数名>[=<默认值>]
```



## LABEL 定义标签

定义一个 image 标签 Owner，并赋值，其值为变量 Name 的值。

```
LABEL Owner=$Name
```



## VOLUME 挂载点

用于在image中创建一个挂在点目录，以挂在Docker host上的卷或其他容器上的卷

如果挂在点目录路径下此前文件存在，docker run命令会在卷挂在完成后将此前的所有文件复制到新挂在的卷中

```
VOLUME ["<路径1>", "<路径2>"...]
```



```
FROM base  
VOLUME ["/tmp/data"] 
```

运行通过该Dockerfile生成image的容器，/tmp/data目录中的数据在容器关闭后，里面的数据还存在。例如另一个容器也有持久化数据的需求，且想使用上面容器共享的/tmp/data目录，那么可以运行下面的命令启动一个容器：

```
docker run -t -i -rm -volumes-from container1 image2 bash 
```







## EXPOSE 声明端口

`EXPOSE <端口1> [<端口2>...]`

EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。



`EXPOSE`指令告诉容器在运行时要监听的端口，但是这个端口是用于多个容器之间通信用的（links），外面的host是访问不到的。要把端口暴露给外面的主机，在启动容器时使用`-p`选项。

```
# expose memcached(s) port
EXPOSE 11211 11212
```



要将 EXPOSE 和在运行时使用 -p <宿主端口>:<容器端口> 区分开来。-p，是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而 EXPOSE 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

```
# 映射一个端口  
EXPOSE port1  
# 相应的运行容器使用的命令  
docker run -p port1 image  
  
# 映射多个端口  
EXPOSE 11211/udp 11211/tcp
# 相应的运行容器使用的命令  
docker run -p port1 -p port2 -p port3 image  
# 还可以指定需要映射到宿主机器上的某个端口号  
docker run -p host_port1:port1 -p host_port2:port2 -p host_port3:port3 image
```





## WORKDIR 指定工作目录

设置指令，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效



```
WORKDIR <工作目录路径>
```

在 Dockerfile 中，这两行 RUN 命令的执行环境根本不同，是两个完全不同的容器。这就是对 Dokerfile 构建分层存储的概念不了解所导致的错误。

```
RUN cd /app
RUN echo "hello" > world.txt
```

每一个 RUN 都是启动一个容器、执行命令、然后提交存储层文件变更。第一层 RUN cd /app 的执行仅仅是当前进程的工作目录变更，一个内存上的变化而已，其结果不会造成任何文件变更。而到第二层的时候，启动的是一个全新的容器，跟第一层的容器更完全没关系，自然不可能继承前一层构建过程中的内存变化。



`WORKDIR指`令用于设置`Dockerfile`中的`RUN`、`CMD`和`ENTRYPOINT`指令执行命令的工作目录(默认为`/`目录)，该指令在`Dockerfile`文件中可以出现多次，如果使用相对路径则为相对于`WORKDIR`上一次的值，例如`WORKDIR /a`，`WORKDIR b`，`RUN pwd`最终输出的当前目录是`/a/b`。（`RUN cd /a/b`，`RUN pwd`是得不到`/a/b`的）



## USER 指定当前用户

设置指令，设置启动容器的用户，默认是root用户

USER 指令和 WORKDIR 相似，都是改变环境状态并影响以后的层。

UID必须是/etc/passwd中某用户的有效UID，否则docker run命令将运行失败

`USER <用户名>`

```
RUN groupadd -r redis && useradd -r -g redis redis
USER redis
RUN [ "redis-server" ]
```



USER 只是帮助你切换到指定用户而已，这个用户必须是事先建立好的，否则无法切换。 





使用 gosu 切换到其他用户执行某个服务

```
# 建立 redis 用户，并使用 gosu 换另一个用户执行命令
RUN groupadd -r redis && useradd -r -g redis redis
# 下载 gosu
RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-amd64" \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true
# 设置 CMD，并以另外的用户执行
CMD [ "exec", "gosu", "redis", "redis-server" ]
```









## HEALTHCHECK 健康检查

设置检查容器健康状况的命令

`HEALTHCHECK [选项] CMD <命令>`


如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令

`HEALTHCHECK NONE`

>--interval=<间隔>：两次健康检查的间隔，默认为 30 秒；
>--timeout=<时长>：健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认 30 秒；
>--retries=<次数>：当连续失败指定次数后，则将容器状态视为 unhealthy，默认 3 次。

*  自 1.12 之后，Docker 提供了 HEALTHCHECK 指令，通过该指令指定一行命令，用这行命令来判断容器主进程的服务状态是否还正常，从而比较真实的反应容器实际状态。


* 增加健康检查来判断其 Web 服务是否在正常工作

```
FROM nginx
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
HEALTHCHECK --interval=5s --timeout=3s \
  CMD curl -fs http://localhost/ || exit 1
```

* 健康检查命令的输出（包括 stdout 以及 stderr）都会被存储于健康状态里，可以用 docker inspect 来查看。

```
$ docker inspect --format '{{json .State.Health}}' web | python -m json.tool
{
    "FailingStreak": 0,
    "Log": [
        {
            "End": "2016-11-25T14:35:37.940957051Z",
            "ExitCode": 0,
            "Output": "<!DOCTYPE html>\n<html>\n<head>\n<title>Welcome to nginx!</title>\n<style>\n    body {\n        width: 35em;\n        margin: 0 auto;\n        font-family: Tahoma, Verdana, Arial, sans-serif;\n    }\n</style>\n</head>\n<body>\n<h1>Welcome to nginx!</h1>\n<p>If you see this page, the nginx web server is successfully installed and\nworking. Further configuration is required.</p>\n\n<p>For online documentation and support please refer to\n<a href=\"http://nginx.org/\">nginx.org</a>.<br/>\nCommercial support is available at\n<a href=\"http://nginx.com/\">nginx.com</a>.</p>\n\n<p><em>Thank you for using nginx.</em></p>\n</body>\n</html>\n",
            "Start": "2016-11-25T14:35:37.780192565Z"
        }
    ],
    "Status": "healthy"
}
```





## ONBUILD 在子镜像中执行

`ONBUILD`指令用来设置一些触发的指令，用于在当该镜像被作为基础镜像来创建其他镜像时(也就是`Dockerfile`中的`FROM`为当前镜像时)执行一些操作，`ONBUILD中`定义的指令会在用于生成其他镜像的`Dockerfile`文件的`FROM`指令之后被执行，上述介绍的任何一个指令都可以用于`ONBUILD`指令，可以用来执行一些因为环境而变化的操作，使镜像更加通用



1. ONBUILD中定义的指令在当前镜像的build中不会被执行。
2. 可以通过查看`docker inspect <image>`命令执行结果的OnBuild键来查看某个镜像ONBUILD指令定义的内容。
3. ONBUILD中定义的指令会当做引用该镜像的Dockerfile文件的FROM指令的一部分来执行，执行顺序会按ONBUILD定义的先后顺序执行，如果ONBUILD中定义的任何一个指令运行失败，则会使FROM指令中断并导致整个build失败，当所有的ONBUILD中定义的指令成功完成后，会按正常顺序继续执行build。
4. ONBUILD中定义的指令不会继承到当前引用的镜像中，也就是当引用ONBUILD的镜像创建完成后将会清除所有引用的ONBUILD指令。
5. ONBUILD指令不允许嵌套，例如`ONBUILD ONBUILD ADD . /data`是不允许的。
6. ONBUILD指令不会执行其定义的FROM或MAINTAINER指令
7. 在ONBUILD指令中使用ADD或COPY指令要小心，因为新构建过程的上下文在缺少指定的源文件时会失败

例如，`Dockerfile`使用如下的内容创建了镜像 image-A ：

```
[...]
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
[...]
```



如果基于 image-A 创建新的镜像时，新的`Dockerfile`中使用`FROM image-A`指定基础镜像时，会自动执行`ONBUILD`指令内容，等价于在后面添加了两条指令。

```
FROM image-A

#Automatically run the following
ADD . /app/src
RUN /usr/local/bin/python-build --dir /app/src
```



## .dockerignore 文件

`.dockerignore`用来忽略上下文目录中包含的一些image用不到的文件，它们不会传送到docker daemon。规则使用go语言的匹配语法。如：

```
$ cat .dockerignore
.git
tmp*
```





## 应用运行前的准备工作

准备工作是和容器 CMD 无关的，无论 CMD 为什么，都需要事先进行一个预处理的工作。这种情况下，可以写一个脚本，然后放入 ENTRYPOINT 中去执行，而这个脚本会将接到的参数（也就是 <CMD>）作为命令，在脚本最后执行。比如官方镜像 redis 中就是这么做的：

```
FROM alpine:3.4
...
RUN addgroup -S redis && adduser -S -G redis redis
...
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD [ "redis-server" ]
```



# example

## mysql 构建过程

下面的`Dockerfile`是MySQL官方镜像的构建过程。从ubuntu基础镜像开始构建，安装mysql-server、配置权限、映射目录和端口，`CMD`在从这个镜像运行到容器时启动mysql。其中`VOLUME`定义的两个可挂载点，用于在host中挂载，因为数据库保存在主机上而非容器中才是比较安全的。

```
#
# MySQL Dockerfile
#
# https://github.com/dockerfile/mysql
#

# Pull base image.
FROM dockerfile/ubuntu

# Install MySQL.
RUN \
  apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -y mysql-server && \
  rm -rf /var/lib/apt/lists/* && \
  sed -i 's/^\(bind-address\s.*\)/# \1/' /etc/mysql/my.cnf && \
  sed -i 's/^\(log_error\s.*\)/# \1/' /etc/mysql/my.cnf && \
  echo "mysqld_safe &" > /tmp/config && \
  echo "mysqladmin --silent --wait=30 ping || exit 1" >> /tmp/config && \
  echo "mysql -e 'GRANT ALL PRIVILEGES ON *.* TO \"root\"@\"%\" WITH GRANT OPTION;'" >> /tmp/config && \
  bash /tmp/config && \
  rm -f /tmp/config

# Define mountable directories.
VOLUME ["/etc/mysql", "/var/lib/mysql"]

# Define working directory.
WORKDIR /data

# Define default command.
CMD ["mysqld_safe"]

# Expose ports.
EXPOSE 3306
```

使用：

```
$ docker build -t="dockerfile/mysql" github.com/dockerfile/mysql

或下载Dockerfile内容再当前目录：
$ docker build -t="dockerfile/mysql" .
```



（提示，上述第一条命令，如果你的host不可以连接Docker Hub，那么需要在启动docker服务时使用`HTTP_PROXY=`——用于build的时更新下载软件，同时执行`docker build`的终端设置`http_proxy`和`https_proxy`用于下载Dockerfile）

运行：

```
$ docker run -d --name mysql -p 3306:3306 dockerfile/mysql
或
$ docker run -it --rm --link mysql:mysql dockerfile/mysql bash -c 'mysql -h $MYSQL_PORT_3306_TCP_ADDR'
```



## 让镜像变成像命令一样使用

* 查看当前公网 IP 的镜像

```
FROM ubuntu:16.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
CMD [ "curl", "-s", "http://ip.cn" ]
```

* 假如我们使用 docker build -t myip . 来构建镜像的话，如果我们需要查询当前公网 IP，只需要执行：

```
$ docker run myip
当前 IP：61.148.226.66 来自：北京市 联通
```

* 如果我们希望加入 -i 这参数

```
$ docker run myip curl -s http://ip.cn -i
```

* 用 ENTRYPOINT 来实现这个镜像：

```
FROM ubuntu:16.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
ENTRYPOINT [ "curl", "-s", "http://ip.cn" ]


$ docker run myip
当前 IP：61.148.226.66 来自：北京市 联通

$ docker run myip -i
HTTP/1.1 200 OK
Server: nginx/1.8.0
Date: Tue, 22 Nov 2016 05:12:40 GMT
Content-Type: text/html; charset=UTF-8
Vary: Accept-Encoding
X-Powered-By: PHP/5.6.24-1~dotdeb+7.1
X-Cache: MISS from cache-2
X-Cache-Lookup: MISS from cache-2:80
X-Cache: MISS from proxy-2_6
Transfer-Encoding: chunked
Via: 1.1 cache-2:80, 1.1 proxy-2_6:8006
Connection: keep-alive

当前 IP：61.148.226.66 来自：北京市 联通

```



### tomcat

#### 构建Dockerfile

```
# 指定基于的基础镜像
FROM ubuntu:13.10  

# 维护者信息
MAINTAINER zhangjiayang "zhangjiayang@sczq.com.cn"  
  
# 镜像的指令操作
# 获取APT更新的资源列表
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe"> /etc/apt/sources.list
# 更新软件
RUN apt-get update  
  
# Install curl  
RUN apt-get -y install curl  
  
# Install JDK 7  
RUN cd /tmp &&  curl -L 'http://download.oracle.com/otn-pub/java/jdk/7u65-b17/jdk-7u65-linux-x64.tar.gz' -H 'Cookie: oraclelicense=accept-securebackup-cookie; gpw_e24=Dockerfile' | tar -xz  
RUN mkdir -p /usr/lib/jvm  
RUN mv /tmp/jdk1.7.0_65/ /usr/lib/jvm/java-7-oracle/  
  
# Set Oracle JDK 7 as default Java  
RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-7-oracle/bin/java 300     
RUN update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-7-oracle/bin/javac 300     

# 设置系统环境
ENV JAVA_HOME /usr/lib/jvm/java-7-oracle/  
  
# Install tomcat7  
RUN cd /tmp && curl -L 'http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.8/bin/apache-tomcat-7.0.8.tar.gz' | tar -xz  
RUN mv /tmp/apache-tomcat-7.0.8/ /opt/tomcat7/  
  
ENV CATALINA_HOME /opt/tomcat7  
ENV PATH $PATH:$CATALINA_HOME/bin  

# 复件tomcat7.sh到容器中的目录 
ADD tomcat7.sh /etc/init.d/tomcat7  
RUN chmod 755 /etc/init.d/tomcat7  
  
# Expose ports.  指定暴露的端口
EXPOSE 8080  
  
# Define default command.  
ENTRYPOINT service tomcat7 start && tail -f /opt/tomcat7/logs/catalina.out
```



#### tomcat7.sh

```
export JAVA_HOME=/usr/lib/jvm/java-7-oracle/  
export TOMCAT_HOME=/opt/tomcat7  
  
case $1 in  
start)  
  sh $TOMCAT_HOME/bin/startup.sh  
;;  
stop)  
  sh $TOMCAT_HOME/bin/shutdown.sh  
;;  
restart)  
  sh $TOMCAT_HOME/bin/shutdown.sh  
  sh $TOMCAT_HOME/bin/startup.sh  
;;  
esac  
exit 0
```



#### 构建镜像

```
docker build -t wechat-tomcat
docker run -d -p 8090:8080 wechat-tomcat 
```



## 构建Wordpress + nginx运行环境

```
# 指定基于的基础镜像
FROM ubuntu:14.04

# 维护者信息
MAINTAINER Eugene Ware <eugene@noblesamurai.com>

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get -y upgrade

# Basic Requirements
RUN apt-get -y install mysql-server mysql-client nginx php5-fpm php5-mysql php-apc pwgen python-setuptools curl git unzip

# Wordpress Requirements
RUN apt-get -y install php5-curl php5-gd php5-intl php-pear php5-imagick php5-imap php5-mcrypt php5-memcache php5-ming php5-ps php5-pspell php5-recode php5-sqlite php5-tidy php5-xmlrpc php5-xsl

# mysql config， 配置MySQL运行参数
RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

# nginx config， 配置Nginx运行参数
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# php-fpm config
RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
RUN sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php5/fpm/pool.d/www.conf
RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;

# nginx site conf，将本地Nginx配置文件复制到容器中的目录
ADD ./nginx-site.conf /etc/nginx/sites-available/default

# Supervisor Config
RUN /usr/bin/easy_install supervisor
RUN /usr/bin/easy_install supervisor-stdout
ADD ./supervisord.conf /etc/supervisord.conf

# Install Wordpress
ADD https://wordpress.org/latest.tar.gz /usr/share/nginx/latest.tar.gz
RUN cd /usr/share/nginx/ && tar xvf latest.tar.gz && rm latest.tar.gz
RUN mv /usr/share/nginx/html/5* /usr/share/nginx/wordpress
RUN rm -rf /usr/share/nginx/www
RUN mv /usr/share/nginx/wordpress /usr/share/nginx/www
RUN chown -R www-data:www-data /usr/share/nginx/www

# Wordpress Initialization and Startup Script
ADD ./start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 3306
EXPOSE 80

# volume for mysql database and wordpress install
VOLUME ["/var/lib/mysql", "/usr/share/nginx/www"]

# 容器启动时执行命令
CMD ["/bin/bash", "/start.sh"]

```



## 构建Ruby on Rails环境

```
# 指定基础镜像
FROM fcat/ubuntu-universe:12.04

# development tools
RUN apt-get -qy install git vim tmux

# ruby 1.9.3 and build dependencies
RUN apt-get -qy install ruby1.9.1 ruby1.9.1-dev build-essential libpq-dev libv8-dev libsqlite3-dev

# bundler
RUN gem install bundler

# create a "rails" user
# the Rails application will live in the /rails directory
RUN adduser --disabled-password --home=/rails --gecos "" rails

# copy the Rails app
# we assume we have cloned the "docrails" repository locally
#  and it is clean; see the "prepare" script
ADD docrails/guides/code/getting_started /rails

# Make sure we have rights on the rails folder
RUN chown rails -R /rails

# copy and execute the setup script
# this will run bundler, setup the database, etc.
ADD scripts/setup /setup
RUN su rails -c /setup

# copy the start script
ADD scripts/start /start

EXPOSE 3000

# 创建用户
USER rails

# 设置容器启动命令
CMD /start

```



## 构建Nginx运行环境

```
# 指定基础镜像
FROM sameersbn/ubuntu:14.04.20161014

# 维护者信息
MAINTAINER sameer@damagehead.com

# 设置环境
ENV RTMP_VERSION=1.1.10 \
    NPS_VERSION=1.11.33.4 \
    LIBAV_VERSION=11.8 \
    NGINX_VERSION=1.10.1 \
    NGINX_USER=www-data \
    NGINX_SITECONF_DIR=/etc/nginx/sites-enabled \
    NGINX_LOG_DIR=/var/log/nginx \
    NGINX_TEMP_DIR=/var/lib/nginx \
    NGINX_SETUP_DIR=/var/cache/nginx

# 设置构建时变量，镜像建立完成后就失效
ARG BUILD_LIBAV=false
ARG WITH_DEBUG=false
ARG WITH_PAGESPEED=true
ARG WITH_RTMP=true

# 复制本地文件到容器目录中
COPY setup/ ${NGINX_SETUP_DIR}/
RUN bash ${NGINX_SETUP_DIR}/install.sh

# 复制本地配置文件到容器目录中
COPY nginx.conf /etc/nginx/nginx.conf
COPY entrypoint.sh /sbin/entrypoint.sh

# 运行指令
RUN chmod 755 /sbin/entrypoint.sh

# 允许指定的端口
EXPOSE 80/tcp 443/tcp 1935/tcp

# 指定网站目录挂载点
VOLUME ["${NGINX_SITECONF_DIR}"]

ENTRYPOINT ["/sbin/entrypoint.sh"]
CMD ["/usr/sbin/nginx"]
```



## 构建Postgres镜像

```
# 指定基础镜像
FROM sameersbn/ubuntu:14.04.20161014

# 维护者信息
MAINTAINER sameer@damagehead.com

# 设置环境变量
ENV PG_APP_HOME="/etc/docker-postgresql"\
    PG_VERSION=9.5 \
    PG_USER=postgres \
    PG_HOME=/var/lib/postgresql \
    PG_RUNDIR=/run/postgresql \
    PG_LOGDIR=/var/log/postgresql \
    PG_CERTDIR=/etc/postgresql/certs

ENV PG_BINDIR=/usr/lib/postgresql/${PG_VERSION}/bin \
    PG_DATADIR=${PG_HOME}/${PG_VERSION}/main

# 下载PostgreSQL
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
 && echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' > /etc/apt/sources.list.d/pgdg.list \
 && apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y acl \
      postgresql-${PG_VERSION} postgresql-client-${PG_VERSION} postgresql-contrib-${PG_VERSION} \
 && ln -sf ${PG_DATADIR}/postgresql.conf /etc/postgresql/${PG_VERSION}/main/postgresql.conf \
 && ln -sf ${PG_DATADIR}/pg_hba.conf /etc/postgresql/${PG_VERSION}/main/pg_hba.conf \
 && ln -sf ${PG_DATADIR}/pg_ident.conf /etc/postgresql/${PG_VERSION}/main/pg_ident.conf \
 && rm -rf ${PG_HOME} \
 && rm -rf /var/lib/apt/lists/*

COPY runtime/ ${PG_APP_HOME}/
COPY entrypoint.sh /sbin/entrypoint.sh
RUN chmod 755 /sbin/entrypoint.sh

# 指定端口
EXPOSE 5432/tcp

# 指定数据挂载点
VOLUME ["${PG_HOME}", "${PG_RUNDIR}"]

# 切换目录
WORKDIR ${PG_HOME}

# 设置容器启动时执行命令
ENTRYPOINT ["/sbin/entrypoint.sh"]
```

