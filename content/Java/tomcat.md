---
title: "tomcat"
date: 2020-01-31 21:03
---
[toc]



# Tomcat

基于Java的Web应用服务器软件





## 安装

Java 配置

```
wget https://mirrors.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz

vim /etc/profile
export JRE_HOME=/root/java_web/jdk1.8.0_201/jre
export JAVA_HOME=/root/java_web/jdk1.8.0_201
export JAVA_BIN=/root/java_web/jdk1.8.0_201/bin
PATH=$PATH:$JAVA_BIN
```



```
wget https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.65/bin/apache-tomcat-7.0.65.tar.gz

# 配置环境变量 CATALINA_HOME
export CATALINA_HOME=/root/java_web/apache-tomcat-7.0.65
export TOMCAT_HOME=/root/java_web/apache-tomcat-7.0.65
```



## 启动

```
$CATALINA_HOME/bin/startup.sh
```



# 目录结构

```
bin  # 可执行文件
conf	# 配置文件
lib		# tomcat 的依赖库
LICENSE
logs		# 日志
NOTICE
RELEASE-NOTES
RUNNING.txt
temp		# 临时文件夹
webapps		# 默认的应用部署目录i
work	# 供web 应用使用
```



## bin 

含有启动脚本

通过环境变量JAVA_OPTS 配置JVM的启动参数





## conf

server.xml 重要的配置文件

```
<Server>
    <Service>	#可以多个
        <Connector> #可以多个，接收用户请求
            <Engine>	#一个Service 对应一个Engine，处理connector接收到的请求
                <Host>	#可以多个，虚拟主机的概念
                    <Context>	#可以多个，即对应一个Web应用
                    </Context>
                </Host>
            </Engine>
        </Connector>
    </Service>
</Server>
```



### server.xml

<Connector> 用于定义端口号



## Connector

Coyote实现

默认是BIO Connector

完成网络相关处理



## Container

通过Catalina组件实现

执行web应用的代码







## lib

存放Tomcat服务器的jar包



## logs 

存放tomcat服务器的日志文件



## webapps

Web应用的部署目录



## work

Tomcat的工作目录









