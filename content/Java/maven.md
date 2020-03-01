---
title: "maven"
date: 2020-02-01 12:02
---
[toc]



# Maven

管理Java项目构建和依赖管理工具

使用maven后每个jar包只在本地仓库中保存一份，需要jar包的工程只需要维护一个文本形式的jar包的引用

maven会自动把当前jar包所依赖的其他所有jar包全部导入进来



## 特点

maven的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的



## 安装

依赖JDK，需要保证Java安装完成

配置环境变量

```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz

vim /etc/profile
export M2_HOME=/root/java_web/apache-maven-3.6.3
PATH=$PATH:$M2_HOME/bin
```





# pom.xml

maven依据这个文件进行管理



## groupId 组织的域名

项目唯一标识符部分，不能重复

团体的标志，一般使用公司或者组织的域名倒序+当前项目名称

```
<groupId>com.apache</groupId>
```



## artifactId 项目标识符 

项目唯一标识符部分，模块名称，不能重复

不要加点

```
<artifactId>helloworld</artifactId>
```



## version 版本 

项目唯一标识符部分，不能重复

可以添加SNAPSHOT表示快照版本

```
<version>1.0.0-SNAPSHOT</version>
```



## 文件查找gav

以gav三个向量加起来可以查找到对应的jar包

```
/Users/rxu/.m2/repository/org/springframework/boot/spring-boot-starter-json/2.1.12.RELEASE/spring-boot-starter-json-2.1.12.RELEASE-javadoc.jar
```





## packaging 类型

项目的一个属性

```
<packaging>war</packaging>
```



## dependencyManagement 依赖管理

### dependencies 依赖的项目



## parent 父工程

```xml
<parent>
  <groupId>com.xurick.maven</groupId>
  <artifactId>Parent</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  
  <!-- 指定从当前子工程的pom.xml文件出发，查找到父工程的pom.xml的路径 -->
  <relativePath>../Parent/pom.xml</relativePath>
</parent>
```

> 如果子工程的groupId和version和父工程重复，可以删除掉



## modules 聚合

聚合之后可以批量执行maven工程的安装，清理工作

指定模块工程的相对路径即可

```xml
<modules>
  <module>../Module1</module>
  <module>../Module2</module>
</modules>
```





# Maven 生命周期

maven的生命周期与插件的目标target相互绑定，以完成某个具体的构建任务



## Clean Lifecycle

在进行真正的构建之前进行的一些清理工作



## Default Lifecycle （核心）

构建的核心部分，编译，测试，打包，安装，部署等



重要的常用阶段

```
compile 编译项目源代码

test-compile 编译测试源代码

test 使用合适的单元测试框架运行测试，这些测试代码不会被打包或者部署

package 接受编译好的代码，打包可发布的格式，如jar

install 将包安装至本地仓库，以让其他项目依赖
```



## Site Lifecycle

生成项目报告，站点，发布站点

# Maven 仓库

maven核心程序只负责宏观调度， 默认会到本地仓库中查找插件 `~/.m2/repository`

如果本地仓库没有，会从远程中央仓库下载  



## localRepository

设置仓库位置

```
vim /root/java_web/apache-maven-3.6.3/conf/setting.xml


<localRepository>/opt</localRepository>
```



# 基本命令

```
mvn archetype:generate 使用模板生成项目
mvn compile 编译源代码
mvn test 单元测试
mvn package 打包war
mvn deploy
mvn site 生成项目相关站点，在线文档
mvn clean 清理
mvn install 安装本地仓库
```



## example

```
mvn archetype:generate -DgroupId=com.netease.restaurant -DartifactId=Restaurant -Dpackage=com.netease -Dversion=1.0.0-SNAPSHOT -DarchetypeArtifactId=maven-archetype-webapp 
```



