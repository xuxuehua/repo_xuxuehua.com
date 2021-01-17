---
title: "airflow"
date: 2021-01-07 10:45
---
[toc]



# Airflow

Airflow 是一个编排、调度和监控workflow的平台，由Airbnb开源，现在在Apache Software Foundation 孵化。AirFlow 将workflow编排为tasks组成的DAGs，调度器在一组workers上按照指定的依赖关系执行tasks。同时，Airflow 提供了丰富的命令行工具和简单易用的用户界面以便用户查看和操作，并且Airflow提供了监控和报警系统。

Airflow 使用 DAG (有向无环图) 来定义工作流，配置作业依赖关系非常方便，从管理方便和使用简单角度来讲，AirFlow远超过其他的任务调度工具。



## expectation

1. How Airflow handle logs. Could it  sync and display spark task log in real time? Where the log store.
2. How Airflow handle kill task. Could it kill the spark task?
3. How Airflow handle task rerun. Could it show each task attempts?
4. Cal Airflow skip task?
5. Is it possible to adapt the loop executor in BDP on Airflow? By inherit an operator?
6. Could Airflow construct and run a Graph like current BDP?





1. We can only see livy operator logs in web including the process of calling livy API, but no logs in spark task.
2. A task can be marked as FAILED status or be cleared in the web, and both can trigger a kill. It also can kill the yarn application of it.
3. Click CLEAR button in web can trigger to rerun a task with some options. Each task attempts can be recorded and review. The picture shows what operations we can do via the web to control a task.
4. Airflow can skip a task by marking it FAILED or SUCCESS. but as far as I know, it dose not have a skip status.
5. We can pass input parameters like start_date and end_date which can be used in our task to loop historical days. Also we can inherit an operator to handle this but maybe not easy.
6. Airflow can construct and run a Graph like current BDP. The logic of composing workflow from tasks is the same. There is a picture of complex DAG below. It looks like more irregular and disorder than BDP workflow.



## 特点

- 灵活易用，AirFlow 本身是 Python 编写的，且工作流的定义也是 Python 编写，有了 Python胶水的特性，没有什么任务是调度不了的，有了开源的代码，没有什么问题是无法解决的，你完全可以修改源码来满足个性化的需求，而且更重要的是代码都是 –human-readable 。
- 功能强大，自带的 Operators 都有15+，也就是说本身已经支持 15+ 不同类型的作业，而且还是可自定义 Operators，什么 shell 脚本，python，mysql，oracle，hive等等，无论不传统数据库平台还是大数据平台，统统不在话下，对官方提供的不满足，完全可以自己编写 Operators。
- 优雅，作业的定义很简单明了, 基于 jinja 模板引擎很容易做到脚本命令参数化，web 界面更是也非常 –human-readable ，谁用谁知道。
- 极易扩展，提供各种基类供扩展, 还有多种执行器可供选择，其中 CeleryExcutor 使用了消息队列来编排多个工作节点(worker), 可分布式部署多个 worker ，AirFlow 可以做到无限扩展。
- 丰富的命令工具，你甚至都不用打开浏览器，直接在终端敲命令就能完成测试，部署，运行，清理，重跑，追数等任务，想想那些靠着在界面上不知道点击多少次才能部署一个小小的作业时，真觉得AirFlow真的太友好了。





# 架构

Airflow由多个部分组成。 并非必须部署所有这些工具（例如，对于Flower和Webserver 可不做部署）。但是在设置和调试系统时，它们都会派上了用场。这些组成部分有以下几种 –

| Flower 与Webserver | 用于监测和与Airflow集群交互的用户前端 |
| ------------------ | ------------------------------------- |
| Scheduler          | 连续轮询DAG并安排任务。 监视执行      |
| Workers            | 从队列执行任务，并报告回队列          |
| Shared Filesystem  | 在所有群集节点之间同步DAG             |
| Queue              | 可靠地调度计划任务和任务结果          |
| Metadb             | 存储执行历史记录，工作流和其他元数据  |



![image-20210110113413227](airflow.assets/image-20210110113413227.png)



## Scheduler

调度器。调度器是整个airlfow的核心枢纽，负责发现用户定义的dag文件，并根据定时器将有向无环图转为若干个具体的dagrun，并监控任务状态。

**Dag** 有向无环图。有向无环图用于定义任务的任务依赖关系。任务的定义由算子operator进行，其中，BaseOperator是所有算子的父类。

**Dagrun** 有向无环图任务实例。在调度器的作用下，每个有向无环图都会转成任务实例。不同的任务实例之间用dagid/ 执行时间（execution date）进行区分。

**Taskinstance** dagrun下面的一个任务实例。具体来说，对于每个dagrun实例，算子（operator）都将转成对应的Taskinstance。由于任务可能失败，根据定义调度器决定是否重试。不同的任务实例由 dagid/执行时间（execution date）/算子/执行时间/重试次数进行区分。

**Executor** 任务执行器。每个任务都需要由任务执行器完成。BaseExecutor是所有任务执行器的父类。

**LocalTaskJob** 负责监控任务与行，其中包含了一个重要属性taskrunner。

**TaskRunner** 开启子进程，执行任务。



# DAG

A workflow as a Directed Acyclic Graph with multiple task which can be executed independently 









# AWS Managed Apache Airflow

the S3 bucket and the folder to load my **DAG code**. The bucket name must start with `airflow-`

Optionally, I can specify a plugins file and a requirements file:

- The **plugins** file is a ZIP file containing the plugins used by my DAGs.
- The **requirements** file describes the Python dependencies to run my DAGs.



