---
title: "jenkins"
date: 2018-10-16 11:04
---


[TOC]


# jenkins

jenkins是一个广泛用于持续构建的可视化web工具，持续构建说得更直白点，就是各种项目的"自动化"编译、打包、分发部署。jenkins可以很好的支持各种语言（比如：java, c#, php等）的项目构建，也完全兼容ant、maven、gradle等多种第三方构建工具，同时跟svn、git能无缝集成，也支持直接与知名源代码托管网站，比如github、bitbucket直接集成。



## requirements

```
256 MB RAM
1GB space

Java 8
Java Runtime Environment or Java Development Kit
```

Docker Hardware

```
1GB RAM
10GB space
```



## 安装

### docker

```
docker pull jenkins/jenkins:lts
docker run --detach --publish 8080:8080: --volumn jenkins_home:/var/jenkins_home --name jenkins jenkins/jenkins:lts

docker exec jenkins cat /var/jenkins_home/secrets?initialAdminPassword
```





### Ubuntu 

WAR file 

The Web application ARchive (WAR) file version of Jenkins can be installed on any operating system or platform that supports Java.

```
apt install default-jre
sudo apt-get install openjdk-8-jdk
sudo update-alternatives --config java (set to java 8)

wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
```

```
[Unit]
Description=Jenkins Daemon

[Service]
ExecStart=/usr/bin/java -jar /home/jenkins_user/jenkins.war
User=jenkins_user

[Install]
WantedBy=multi-user.target
```



/etc/systemd/system/jenkins.service

```
[Unit]
Description=Jenkins Daemon

[Service]
ExecStart=/usr/bin/java -jar /home/jenkins_user/jenkins.war
User=jenkins_user

[Install]
WantedBy=multi-user.target
```

```
systemctl start jenkins.service     
systemctl stop jenkins.service
systemctl restart jenkins.service
systemctl enable jenkins.service                                      
systemctl disable jenkins.service 
```

Browse to `http://localhost:8080` and wait until the **Unlock Jenkins** page appears



### CentOS

```
sudo yum install java-1.8.0-openjdk-devel

curl --silent --location http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo | sudo tee /etc/yum.repos.d/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```





## Plugins

### Email extension & Extended E-mail Notification

```
smtp.163.com
username
password  #这里是163的授权码
smtp port 25 
```





### Role-based Authorization Strategy (用户权限)

Installed and then go to "Configure Global Security"

```
Authorization -> Role-Based Strategy -> Apply -> Save
```



Go to 'Manage and Assign Roles'





## https

Securing via HTTPS
Install nginx if not already done
replace the contents of /etc/nginx/sites-enabled/default with the following

```
server {
    listen 80;
    server_name jenkins.xurick.com;
    return 301 https://$host$request_uri;
}

server {

    listen 443;
    server_name jenkins.xurick.com;
     
    ssl_certificate           /var/lib/jenkins/xurick.com.cer;
    ssl_certificate_key       /var/lib/jenkins/xurick.com.key;
     
    ssl on;
    ssl_session_cache  builtin:1000  shared:SSL:10m;
    ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;
    ssl_prefer_server_ciphers on;
     
    access_log            /var/log/nginx/jenkins.access.log;
     
    location / {
     
      proxy_set_header        Host $host;
      proxy_set_header        X-Real-IP $remote_addr;
      proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header        X-Forwarded-Proto $scheme;
     
      # Fix the “It appears that your reverse proxy set up is broken" error.
      proxy_pass          http://localhost:8080;
      proxy_read_timeout  90;
     
      proxy_redirect      http://localhost:8080 https://jenkins.xurick.com;
    }
  }
```

Change the /etc/default/jenkins file to include JAVA_ARGS

`JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpListenAddress=127.0.0.1 --httpPort=​$HTTP_PORT" -ajp13Port=$AJP_PORT"`







# Pipline 

Jenkins 上的工作流程框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排与可视化



Pipeline的实现方式是一套Groovy DSL，任何发布流程都可以表述为一段Groovy脚本，并且Jenkins支持从代码库直接读取脚本，从而实现了Pipeline as Code的理念。



Pipeline支持两种形式，一种是`Declarative`管道，一个是`Scripted`管道



## Declarative 语法

先讲`Declarative Pipeline`，所有声明式管道都必须包含在`pipeline`块中

```
pipeline {
    /* insert Declarative Pipeline here */
}
```

块里面的语句和表达式都是Groovy语法，遵循以下规则：

1. 最顶层规定就是`pipeline { }`
2. 语句结束不需要分号，一行一条语句
3. 块中只能包含`Sections`, `Directives`, `Steps`或者赋值语句
4. 属性引用语句被当成是无参方法调用，比如`input`实际上就是方法`input()`调用



### Sections

```
Sections 在声明式管道中包含一个或多个Directives, Steps
```

#### post

`post` section 定义了管道执行结束后要进行的操作。支持在里面定义很多`Conditions`块： `always`, `changed`, `failure`, `success` 和 `unstable`。 这些条件块会根据不同的返回结果来执行不同的逻辑。

- always：不管返回什么状态都会执行
- changed：如果当前管道返回值和上一次已经完成的管道返回值不同时候执行
- failure：当前管道返回状态值为”failed”时候执行，在Web UI界面上面是红色的标志
- success：当前管道返回状态值为”success”时候执行，在Web UI界面上面是绿色的标志
- unstable：当前管道返回状态值为”unstable”时候执行，通常因为测试失败，代码不合法引起的。在Web UI界面上面是黄色的标志

```
// Declarative //
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
    post { ①
        always { ②
            echo 'I will always say Hello again!'
        }
    }
}
```

#### stages

由一个或多个`stage`指令组成，stages块也是核心逻辑的部分。 我们建议对于每个独立的交付部分（比如`Build`,`Test`,`Deploy`）都应该至少定义一个`stage`指令。比如：

```
// Declarative //
pipeline {
    agent any
    stages { ①
        stage('Example') {
        steps {
            echo 'Hello World'
        }
        }
    }
}
```



#### steps

在`stage`中定义一系列的`step`来执行命令。

```
// Declarative //
pipeline {
    agent any
    stages {
        stage('Example') {
            steps { ①
                echo 'Hello World'
            }
        }
    }
}
```



##### multiline-command

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
    }
}
```



##### 含脚本

```
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    sh '/var/jenkins_home/scripts/fibonacci.sh 5'
                }
                timeout(time: 1, unit: 'MINUTES') {
                    sh '/var/jenkins_home/scripts/fibonacci.sh 32'
                }
            }
        }
    }
}
```



### Directives

jenkins中的各种指令

#### agent

`agent`指令指定整个管道或某个特定的`stage`的执行环境。它的参数可用使用：

1. any - 任意一个可用的agent
2. none - 如果放在pipeline顶层，那么每一个`stage`都需要定义自己的`agent`指令
3. label - 在jenkins环境中指定标签的agent上面执行，比如`agent { label 'my-defined-label' }`
4. node - `agent { node { label 'labelName' } }` 和 label一样，但是可用定义更多可选项
5. docker - 指定在docker容器中运行
6. dockerfile - 使用源码根目录下面的`Dockerfile`构建容器来运行

#### environment

`environment`定义键值对的环境变量

```
// Declarative //
pipeline {
    agent any
    environment { ①
        CC = 'clang'
    }
    stages {
        stage('Example') {
            environment { ②
                AN_ACCESS_KEY = credentials('my-prefined-secret-text') ③
            }
            steps {
                sh 'printenv'
            }
        }
    }
}
```



#### options

还能定义一些管道特定的选项，介绍几个常用的：

- skipDefaultCheckout - 在`agent`指令中忽略源码`checkout`这一步骤。
- timeout - 超时设置`options { timeout(time: 1, unit: 'HOURS') }`
- retry - 直到成功的重试次数`options { retry(3) }`
- timestamps - 控制台输出前面加时间戳`options { timestamps() }`

#### parameters

参数指令，触发这个管道需要用户指定的参数，然后在`step`中通过`params`对象访问这些参数。

```
// Declarative //
pipeline {
    agent any
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    }
    stages {
        stage('Example') {
            steps {
                echo "Hello ${params.PERSON}"
            }
        }
    }
}
```



#### triggers

触发器指令定义了这个管道何时该执行，一般我们会将管道和GitHub、GitLab、BitBucket关联， 然后使用它们的webhooks来触发，就不需要这个指令了。如果不适用`webhooks`，就可以定义两种`cron`和`pollSCM`

- cron - linux的cron格式`triggers { cron('H 4/* 0 0 1-5') }`
- pollSCM - jenkins的`poll scm`语法，比如`triggers { pollSCM('H 4/* 0 0 1-5') }`

```
// Declarative //
pipeline {
    agent any
    triggers {
        cron('H 4/* 0 0 1-5')
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

#### stage

`stage`指令定义在`stages`块中，里面必须至少包含一个`steps`指令，一个可选的`agent`指令，以及其他stage相关指令。

```
// Declarative //
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```



#### tools

定义自动安装并自动放入`PATH`里面的工具集合

```
// Declarative //
pipeline {
    agent any
    tools {
        maven 'apache-maven-3.0.1' ①
    }
    stages {
        stage('Example') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}
```



注：① 工具名称必须预先在Jenkins中配置好了 → Global Tool Configuration.

#### 内置条件

- branch - 分支匹配才执行 `when { branch 'master' }`
- environment - 环境变量匹配才执行 `when { environment name: 'DEPLOY_TO', value: 'production' }`
- expression - groovy表达式为真才执行 `expression { return params.DEBUG_BUILD } }`

```
// Declarative //
pipeline {
    agent any
    stages {
        stage('Example Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            when {
                branch 'production'
            }
            echo 'Deploying'
        }
    }
}
```



### parameterized 带参数

```
pipeline {
    agent any
    parameters {
        string(name: 'Greeting', defaultValue: 'Hello', description: 'How should I greet the world?')
    }
    stages {
        stage('Example') {
            steps {
                echo "${params.Greeting} World!"
            }
        }
    }
}
```



### Steps

这里就是实实在在的执行步骤了，每个步骤step都具体干些什么东西， 前面的`Sections`、`Directives`算控制逻辑和环境准备，这里的就是真实执行步骤。

这部分内容最多不可能全部讲完，[官方Step指南](https://jenkins.io/doc/pipeline/steps/) 包含所有的东西。

`Declared Pipeline`和`Scripted Pipeline`都能使用这些step，除了下面这个特殊的`script`。

一个特殊的step就是`script`，它可以让你在声明管道中执行脚本，使用groovy语法，这个非常有用：

```
// Declarative //
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
                script {
                    def browsers = ['chrome', 'firefox']
                    for (int i = 0; i < browsers.size(); ++i) {
                        echo "Testing the ${browsers[i]} browser"
                    }
                }
                script {
                    // 一个优雅的退出pipeline的方法，这里可执行任意逻辑
                    if( $VALUE1 == $VALUE2 ) {
                       currentBuild.result = 'SUCCESS'
                       return
                    }
                }
            }
        }
    }
}
```

最后列出来一个典型的`Scripted Pipeline`：

```
node('master') {
    checkout scm

    stage('Build') {
        docker.image('maven:3.3.3').inside {
            sh 'mvn --version'
        }
    }

    stage('Deploy') {
        if (env.BRANCH_NAME == 'master') {
            echo 'I only execute on the master branch'
        } else {
            echo 'I execute elsewhere'
        }
    }
}
```



可以看到，`Scripted Pipeline`没那么多东西，就是定义一个`node`， 里面多个`stage`，里面就是使用Groovy语法执行各个`step`了，非常简单和清晰，也非常灵活。



#### 全局变量引用

除了代码片段生成器之外，Pipeline还提供了一个内置的“ 全局变量引用”。像Snippet Generator一样，它也是由插件动态填充的。与代码段生成器不同的是，全局变量引用仅包含Pipeline提供的变量的文档，这些变量可用于Pipeline。

在Pipeline中默认提供的变量是：

- ENV

  脚本化Pipeline可访问的环境变量，例如： `env.PATH`或`env.BUILD_ID`。请参阅内置的全局变量参考 ，以获取管道中可用的完整和最新的环境变量列表。

- PARAMS

  将为Pipeline定义的所有参数公开为只读 [地图](http://groovy-lang.org/syntax.html#_maps)，例如：`params.MY_PARAM_NAME`。

- currentBuild

  可用于发现有关当前正在执行的Pipeline信息，与如属性`currentBuild.result`，`currentBuild.displayName`等等请教内置的全局变量引用 了一个完整的，而且是最新的，可用的属性列表`currentBuild`。





#### Jenkins file

Node: 节点，一个Node就是一个Jenkins节点，或者是Master，或者是Agent，是执行Step的具体运行期环境。

Stage: 阶段，一个Pipeline可以划分为若干个Stage，每个Stage代表一组操作。注意，Stage是一个逻辑分组的概念，可以跨多个Node。即Stage实际上是Step的逻辑分组。

Step: 步骤，可以是创建一个目录、从代码库中checkout代码、执行一个shell命令、构建Docker镜像、将服务发布到Kubernetes集群中。Step由Jenkins和Jenkins各种插件提供。



Stage和Step可以放到一个Node下面执行，不指定就默认在master节点上面执行。 另外Node和Step也能组合成一个Stage



通过编写`Jenkinsfile`将管道代码化，并且纳入到版本管理系统中。比如：

```
// Declarative //
pipeline {
    agent any ①

    stages {
        stage('Build') { ②
            steps { ③
                sh 'make' ④
            }
        }
        stage('Test'){
            steps {
                sh 'make check'
                junit 'reports/**/*.xml' ⑤
            }
        }
        stage('Deploy') {
            steps {
                sh 'make publish'
            }
        }
    }
}

// Script //
node {
    stage('Build') {
        sh 'make'
    }
    stage('Test') {
        sh 'make check'
        junit 'reports/**/*.xml'
    }
    stage('Deploy') {
        sh 'make publish'
    }
}
```

> ① agent 指示Jenkins分配一个执行器和工作空间来执行下面的Pipeline
> ② stage 表示这个Pipeline的一个执行阶段
> ③ steps 表示在这个stage中每一个步骤
> ④ sh 执行指定的命令
> ⑤ junit 是插件`junit[JUnit plugin]`提供的一个管道步骤，用来收集测试报告





##### 常用的Step

###### 切换当前的目录

```
dir('dir1') {
    sh 'pwd'
}
```

如果切换的目录不存在，将会创建这个目录。





###### 签出指定分支或tag的代码

从git中获取指定分支的代码:

```
git url: 'ssh://git@gitlab.frognew.com/demo/apidemo.git', branch: 'develop'
```

如果需要获取指定tag的代码，需要使用Pipeline: SCM Step的checkout：

```
checkout scm: [$class: 'GitSCM', 
      userRemoteConfigs: [[url: 'ssh://git@gitlab.frognew.com/demo/apidemo.git']], 
      branches: [[name: "refs/tags/1.1.0"]]], changelog: false, poll: false
```





###### 修改当前构建的名称和描述

```
script {
    currentBuild.displayName = "#${BUILD_NUMBER}(apidemo)"
    currentBuild.description = "publish apidemo"
}
```



`Jenkinsfile`是一个包含Jenkins Pipeline定义的文本文件，并被检入源代码控制。考虑以下Pipeline，实施基本的三阶段连续输送Pipeline。

```
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```

> 需要的代理指令指示Jenkins为Pipeline分配一个执行器和工作区。没有agent指令，不仅声明Pipeline无效，所以不能做任何工作！默认情况下，该agent伪指令确保源存储库已被检出并可用于后续阶段的步骤



Toggle Scripted Pipeline *(Advanced)*

```
Jenkinsfile (Scripted Pipeline)
node {
    stage('Build') {
        echo 'Building....'
    }
    stage('Test') {
        echo 'Building....'
    }
    stage('Deploy') {
        echo 'Deploying....'
    }
}
```

并非所有的Pipeline都将具有相同的三个阶段，但是对于大多数项目来说，这是一个很好的起点。



##### ENV 环境变量

Jenkins  Pipeline通过全局变量公开环境变量，该变量`env`可从任何地方获得`Jenkinsfile`。假设Jenkins主机正在运行，在本地主机：8080 / pipeline-syntax / globals＃env中记录了可从Jenkins Pipeline中访问的环境变量的完整列表 `localhost:8080`，其中包括：

- BUILD_ID

  当前版本ID，与Jenkins版本1.597+中创建的构建相同，为BUILD_NUMBER

- JOB_NAME

  此构建项目的名称，如“foo”或“foo / bar”。

- JENKINS_URL

  完整的Jenkins网址，例如example.com:port/jenkins/（注意：只有在“系统配置”中设置了Jenkins网址时才可用）

参考或使用这些环境变量可以像访问Groovy Map中的任何键一样完成 ，例如：

```
Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
            }
        }
    }
}
```

Toggle Scripted Pipeline *(Advanced)*

```
Jenkinsfile (Scripted Pipeline)
node {
    echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
}
```



设置环境变量：

```
// Declarative //
pipeline {
    agent any
    environment {
        CC = 'clang'
    }
    stages {
        stage('Example') {
            environment {
                DEBUG_FLAGS = '-g'
            }
            steps {
                sh 'printenv'
            }
        }
    }
}
```



##### 使用多个agent

```
// Declarative //
pipeline {
    agent none
    stages {
        stage('Build') {
            agent any
            steps {
                checkout scm
                sh 'make'
                stash includes: '**/target/*.jar', name: 'app' ①
            }
        }
        stage('Test on Linux') {
            agent { ②
                label 'linux'
            }
            steps {
                unstash 'app' ③
                sh 'make check'
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
        stage('Test on Windows') {
            agent {
                label 'windows'
            }
            steps {
                unstash 'app'
                bat 'make check' ④
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
    }
}
```

上面的例子，在任一台机器上面做Build操作，并通过`stash`命令保存文件，然后分别在两台agent机器上面做测试。 注意这里所有步骤都是串行执行的。





##### Multibranch Pipeline

多分支管道可以让你在同一个项目中，对每个分支定义一个执行管道。Jenkins或自动发现、管理并执行包含`Jenkinsfile`文件的分支。

这个在前面一篇已经演示过怎样创建这样的Pipeline了，就不再多讲。











# Example

## webhook

```
# We'll need to get a personal API key from our git repo
#1, create a fork of this repository:

https://github.com/robertstarmer/jenkins-git.git

# creating

https://github.com/{your_github_user_name}/jenkins-git.git
#2, in github, get a personal access token:

https://github.com/settings/tokens

#3, when creating the token, add:
admin:repo_hook
repo:*
notifications:*

#4 after configuring jenkins, verify that the webhook
# was created
https://github.com/{your_github_user_name}/jenkins-git/settings/hooks

```



## jenkins node

```
# Setup jenkins master and slave unix machines
#0 launch a new VM or bare metal machine to support
#  the agent machine.  This would be in much the same
#  way as in module 02_01, and a small Ubuntu 18.04
#  VM is recommended (1 core, 1G memory, 10Gdisk is plenty)
#  This VM should have docker installed as well, along with
#  the Jenkins user using the instructions from module 02_01

#1 create an ssh public/private key pair on the master
#  as the jenkins user

ssh-keygen -t rsa -N '' -f ~/.ssh/id_rsa

#2 copy the ~/.ssh/id_rsa.pub to the worker node(s)

# on the master
cat ~/.ssh/id_rsa.pub

# on the worker
cat >> ~/.ssh/authorized_keys <<EOF
ssh-rsa KEY...
EOF

# confirm that you can log in as the jenkins user on
# the worker machine from the master machine

ssh jenkins@worker

#3 It may be that Jenkins is unable to install Java on your worker
#  node, so we'll just make sure it's already installed
#  Log in to the worker as root, and add java 8

add-apt-repository ppa:webupd8team/java
apt-get update
apt install oracle-java8-installer

# Note that you have to manually accept the Oracle license
# as a part of this process
```



## Docker agent

```
# Adding Docker for agents
#1 Configure the target system docker for web/REST access
#  add the following to the Start or ExecStart parameter of 
#  /lib/systemd/system/docker.service
-H tcp://0.0.0.0:4243

#  then restart the docker service
systemctl daemon-reload
systemctl restart docker

#2 Now we can configure jenkins to reach out to the new
#  docker worker. We'll need a docker image to point to
#  for the agent:
jenkins/jnlp-slave

#3 And then we can create a new project/job and add a docker
#  resource to it
pipeline {
    agent {
        docker { image 'node:9-alpine' }
    }
    stages {
        stage('Test'){
            steps {
                sh 'node --version'
            }
        }
    }
}#2 Now we can configure jenkins to reach out to the new
#  docker worker. We'll need a docker image to point to
#  for the agent:
jenkins/jnlp-slave

#3 And then we can create a new project/job and add a docker
#  resource to it
pipeline {
    agent {
        docker { image 'node:9-alpine' }
    }
    stages {
        stage('Test'){
            steps {
                sh 'node --version'
            }
        }
    }
}
```





## Trigger build via REST API

```
# Triggering a build via REST
#1 We need a token from the Jenkins UI:
Admin->Configure->Show API Token

#2 We need a CSRF crumb
curl --user "admin:TOKEN" 'http://jenkins.kumulus.co:8080/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)'
# grab the entire string from Jenkins-Crumb:...

#3 We can now trigger a build
curl -X POST http://jenkins.kumulus.co:8080/job/multi-step/build --user admin:TOKEN --data-urlencode json='' -H "Jenkins-Crumb:....."

```

```
# Get job status from REST
#1 we need a token from the Jenkins UI:
Admin->Configure->Show API Token

#2 we can now request json formatted output
#  for readability, if we have python installed
#  we can forward the data through a simple formatter
curl  -s --user admin:TOKEN http://jenkins.kumulus.co:8080/job/multi-step/lastBuild/api/json | python -mjson.tool
```



## Securing secret credential 

```
Credential -> Stores scoped to Jenkins -> Global (Domain) 
```

> ID 对应 jenkins-secret-text 文件名



```
pipeline {
    agent any
    environment {
       SECRET=credentials('jenkins-secret-text')
    }
    stages {
        stage('Build') {
            steps {
                echo "${env.SECRET}"
            }
        }
    }
}
```



## 中途中止选项 Pipeline gates

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
            }
        }
        stage('BuildMore'){
            steps {
                input message: "Shall we build more?"
                sh '''
                    echo "We are approved; continue!"
                    ls -lah
                '''
            }
        }
    }
}
```





# 术语

## SCM

Source control management



## CI/CD concept

```
Develop		  Test		 Stage	    	Production
Continuous Integration
Continuous              Delivery
Continous 					            Deployment
```



## Global Variable Reference

```
http://sj.xurick.com:8080/job/pipeline/pipeline-syntax/globals
```





